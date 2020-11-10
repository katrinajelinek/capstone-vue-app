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
      <p>Created {{relativeDate(post.created_at)}}</p>
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
      <img :src="offer.image_url" alt="" class="image-fit">
      <p>Message: {{offer.message}}</p>
      <p>Created {{relativeDate(offer.created_at)}}</p>
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
              <input type="file" class="form-control" v-on:change="setFile($event)" ref="fileInput">
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
import moment from "moment";


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
    relativeDate: function (date) {
      return moment(date).fromNow();
    },
    setFile: function(event) {
      if (event.target.files.length > 0) {
        this.image = event.target.files[0];
      }
    },
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
      var formData = new FormData();
      var index = this.user.offers.indexOf(offer);
      formData.append("message", offer.message);
      formData.append("post_id", offer.post_id);
      if (this.image) {
        formData.append("image", this.image);
      }
      axios
        .patch(`/api/offers/${offer.id}`, formData)
        .then((response) => {
          this.user.offers[index] = (response.data);
          this.offerEditAuthentication = null;
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
    destroyOffer: function (offer) {
      axios.delete(`/api/offers/${offer.id}`).then(response => {
        console.log("Success", response.data);
        this.$router.push(`/posts/${offer.post_id}`);
      });
    },
    setSortAttribute: function (attribute) {
      this.sortAttribute = attribute;
    },
  },
};
</script>
