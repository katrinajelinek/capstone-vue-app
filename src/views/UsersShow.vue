<template>
  <div class="users-show">
    <img :src="user.image_url" alt="" class="image-fit">
    <h3>{{user.first_name}} {{user.last_name}}</h3>
    <div v-if="$parent.getUserId() == user.id">
        <router-link :to="`/users/${user.id}/edit`">Edit Profile</router-link>
    </div>
    <div v-if="$parent.getUserId() == user.id">
        <router-link to="/posts/new">Post a clipping</router-link>
    </div>

    <!-- posts index -->
    <h2>Posts</h2>
    <div v-for="post in orderBy(user.posts, 'created_at')">
      <router-link :to="`/posts/${post.id}`">
       <h3>{{post.plant_type}}</h3>
      </router-link>
      <p>Trade for: {{post.trade_for}}</p>
      <p>Description: {{post.description}}</p>
      <p>Loaction: {{post.location}}</p>
      <h3>Tags:</h3>
      <div v-for="tag in post.tags">
        <p>{{tag.name}}</p>
      </div>
      <p>Created at: {{post.created_at}}</p>
      <div v-if="$parent.getUserId() == post.user_id">
        <router-link :to="`/posts/${post.id}/edit`">Edit</router-link>
      </div> 
    </div>
      

<!-- offers index -->
    <h2>Offers</h2>
    <div v-for="offer in orderBy(user.offers, 'created_at')">
      <router-link :to="`/posts/${offer.post_id}`">
        <h3>Posted on: {{offer.post_title}}</h3>
      </router-link>
      <p>Message: {{offer.message}}</p>
      <img :src="offer.image_url" alt="" class="image-fit">
      <div v-if="$parent.getUserId() == offer.user_id">
        <button v-on:click="offerEditAuthentication = offer.id" >
          Edit
        </button>
        <div v-if="offerEditAuthentication === offer.id">
          <form v-on:submit.prevent="updateOffer(offer)">
            <h2>Update Offer:</h2>
            <ul>
              <li class="text-danger" v-for="error in errors">{{ error }}</li>
            </ul>
            <div class="form-group">
              <label>Message:</label> 
              <input type="text" class="form-control" v-model="offer.message">
            </div>
            <div>
              <label>Image:</label> 
              <input type="text" class="form-control" v-model="offer.imageUrl">
            </div>
            <input type="submit" class="btn btn-primary" value="Update">
          </form>
        </div>
        <button v-on:click="destroyOffer(offer)">
          Delete
        </button>
      </div>
    </div>

  </div>
</template>

<style></style>

<script>
import axios from "axios";
import Vue2Filters from "vue2-filters";


export default {
  mixins: [Vue2Filters.mixin],
  data: function() {
    return {
      user: {},
      errors: [],
      offerEditAuthentication: null,
      sortAttribute: "created_at",
    };
  },
  created: function() {
    this.showUser();
  },
  methods: {
    showUser: function () {
      axios.get(`/api/users/${this.$route.params.id}`).then(response => {
        console.log(response.data);
        this.user = response.data;
      });
    },
    destroyUser: function () {
      axios.delete(`/api/users/${this.user.id}`).then(response => {
        console.log("Success", response.data);
        this.$router.push("/posts");
      });
    },
    updateOffer: function (offer) {
      var params = {
        message: offer.message,
        image_url: offer.imageUrl,
      };
      axios
        .patch(`/api/offers/${offer.id}`, params)
        .then((response) => {
          this.$router.push(`/users/${this.user.id}`);
          this.offerEditAuthentication = null;
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
    destroyOffer: function (offer) {
      axios.delete(`/api/offers/${offer.id}`).then(response => {
        console.log("Success", response.data);
        this.$router.push("/posts");
      });
    },
    setSortAttribute: function (attribute) {
      this.sortAttribute = attribute;
    },
  },
};
</script>
