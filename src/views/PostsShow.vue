<template>
  <div class="posts-show">
      <h1>{{post.plant_type}}</h1>
      <h3>Clipped by: {{post.user.first_name}} {{post.user.last_name}}</h3>
      <img :src="post.image_url" alt="">
      <h2>Trade for: {{post.trade_for}}</h2>
      <p>Description: {{post.description}}</p>
      <p>Loaction: {{post.location}}</p>
      <h3>Tags:</h3>
      <div v-for="tag in tags">
        <p>{{tag.name}}</p>
      </div>
      <p>Created at: {{post.created_at}}</p>
      <div v-if="$parent.getUserId() == post.user_id">
        <button v-on:click="postEditToggle = !postEditToggle">
          Edit
        </button>
      </div> 
      <div v-if="$parent.getUserId() == post.user_id">
        <button v-on:click="destroyPost()">
          Delete
        </button>
      </div> 
      

<!-- offers index -->
      <h2>Offers</h2>
      <div v-for="offer in offers">
        <h3>{{offer.user.first_name}} {{offer.user.last_name}}</h3>
        <p>Message: {{offer.message}}</p>
        <img :src="offer.image_url" alt="">
        <div v-if="$parent.getUserId() == offer.user.id">
          <button v-on:click="offerEditToggle = !offerEditToggle">
            Edit
          </button>
        </div>
        <div v-if="$parent.getUserId() == offer.user.id">
          <button>
            Delete
          </button>
      </div> 
      </div>

      <!-- offers upate -->
      <div v-if="offerEditToggle === true">
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
      offerEditToggle: false,
      postEditToggle: false,
      tags: [],
    };
  },
  created: function() {
    this.showPost();
  },
  methods: {
    showPost: function () {
      axios.get(`/api/posts/${this.$route.params.id}`).then(response => {
        console.log(response.data);
        this.post = response.data;
        this.offers = response.data.offers;
        this.tags = response.data.tags;
      });
    },
    destroyPost: function () {
      axios.delete(`/api/posts/${this.post.id}`).then(response => {
        console.log("Success", response.data);
        this.$router.push("/posts");
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
    // updateOffer: function (offer) {
    //   var params = {
    //     message: this.offer.message,
    //     image_url: this.offer.imageUrl,
    //     post_id: this.post.id,
    //   };
    //   axios
    //     .patch(`/api/offers/${this.offer.id}`, params)
    //     .then((response) => {
    //       this.$router.push(`/posts/${this.post.id}`);
    //     })
    //     .catch(error => {
    //       this.errors = error.response.data.errors;
    //     });
    // },
    // destroyOffer: function () {
    //   axios.delete(`/api/posts/${this.offer.id}`).then(response => {
    //     console.log("Success", response.data);
    //     this.$router.push("/posts");
    //   });
    // }
  },
};
</script>
