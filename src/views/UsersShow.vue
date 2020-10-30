<template>
  <div class="users-show">
    <img :src="user.image_url" alt="">
    <h3>{{user.first_name}} {{user.last_name}}</h3>
    <div v-if="$parent.getUserId() == user.id">
        <router-link to="/posts/new">Post a clipping</router-link>
    </div>

    <!-- posts index -->
    <h2>Posts</h2>
    <div v-for="post in user.posts">
      <h3>{{post.plant_type}}</h3>
      <p>Trade for: {{post.trade_for}}</p>
      <p>Description: {{post.description}}</p>
      <p>Loaction: {{post.location}}</p>
      <h3>Tags:</h3>
      <div v-for="tag in tags">
        <p>{{tag.name}}</p>
      </div>
      <p>Created at: {{post.created_at}}</p>
      <div v-if="$parent.getUserId() == post.user_id">
        <router-link :to="`/posts/${post.id}/edit`">Edit</router-link>
      </div> 
    </div>
      

<!-- offers index -->
    <h2>Offers</h2>
    <div v-for="offer in user.offers">
      <router-link :to="`/posts/${offer.post_id}`">
        <h3>Posted on: {{offer.post_title}}</h3>
      </router-link>
      <p>Message: {{offer.message}}</p>
      <img :src="offer.image_url" alt="">
      <div v-if="$parent.getUserId() == offer.user_id">
        <button v-on:click="offerEditToggle = !offerEditToggle" >
          Edit
        </button>
      </div>
      <div v-if="$parent.getUserId() == offer.user_id">
        <button>
          Delete
        </button>
      </div>
    </div>

    <!-- offers upate -->
    <!-- <div v-if="offerEditToggle === true">
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
    </div> -->

  </div>
</template>

<style></style>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      user: {},
      errors: [],
      selectedOffer: {},
      offerEditToggle: false,
      tags: [],
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
        post_id: this.post.id,
      };
      axios
        .patch(`/api/offers/${this.offer.id}`, params)
        .then((response) => {
          this.$router.push(`/posts/${this.post.id}`);
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
    // destroyOffer: function () {
    //   axios.delete(`/api/offers/${this.offer.id}`).then(response => {
    //     console.log("Success", response.data);
    //     this.$router.push("/posts");
    //   });
    // },
    // selectOffer: function(offer) {
    //   this.selectedOffer = offer;
    // }
  },
};
</script>
