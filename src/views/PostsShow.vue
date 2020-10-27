<template>
  <div class="posts-show">
      <h1>{{post.plant_type}}</h1>
      <h3>Clipped by: {{post.user.first_name}} {{post.user.last_name}}</h3>
      <img :src="post.image_url" alt="">
      <h2>Trade for: {{post.trade_for}}</h2>
      <p>Description: {{post.description}}</p>
      <p>Loaction: {{post.location}}</p>
      <p>Created at: {{post.created_at}}</p> <br> <br>

<!-- offers index -->
      <h2>Offers</h2>
      <div v-for="offer in offers">
        <h3>{{offer.user.first_name}} {{offer.user.last_name}}</h3>
        <p>Message: {{offer.message}}</p>
        <img :src="offer.image_url" alt="">
        <div v-if="$parent.getUserId() == offer.user.id">
          <!-- <button v-on:click="">Edit</button> -->
        </div>
      </div>

<!-- offers new -->
    <form v-on:submit.prevent="createOffer()">
      <h2>Place an Offer:</h2>
      <ul>
        <li class="text-danger" v-for="error in errors">{{ error }}</li>
      </ul>
      <div class="form-group">
        <label>Message:</label> 
        <input type="text" class="form-control" v-model="message">
      </div>
      <div>
        <label>Image:</label> 
        <input type="text" class="form-control" v-model="imageUrl">
      </div>
      <input type="submit" class="btn btn-primary" value="Submit">
    </form>

<!-- offers upate -->
    <form v-on:submit.prevent="updateOffer()">
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
</template>

<style></style>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      post: {},
      offers: [],
      offerUser: [],
      errors: [],
      message: "",
      imageUrl: "",
      post_id: "",
      offer: {},
    };
  },
  created: function() {
    this.postsShow();
  },
  methods: {
    postsShow: function () {
      axios.get(`/api/posts/${this.$route.params.id}`).then(response => {
        console.log(response.data);
        this.post = response.data;
        this.offers = response.data.offers;
      });
    },
    createOffer: function() {
      var params = {
        message: this.message,
        image_url: this.imageUrl,
        post_id: this.post.id,
      };
      axios
        .post("/api/offers", params)
        .then((response) => {
          this.$router.push(`/posts/${this.post.id}`);
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
    updateOffer: function () {
      var params = {
        message: this.message,
        image_url: this.imageUrl,
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
    }
  },
};
</script>
