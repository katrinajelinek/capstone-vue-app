<template>
  <div class="posts-show">
<!-- post info -->
    <img :src="post.image_url" alt="" class="image-fit">
    <h1>{{post.plant_type}}</h1>
    <div v-if="post.offer_accepted">
      <h3>An offer has been accepted</h3>
    </div>
    <h3>Clipped by:</h3>
    <router-link :to="`/users/${post.user_id}`">
      <h3>{{post.user.first_name}} {{post.user.last_name}}</h3>
    </router-link>
    <h2>Trade for: {{post.trade_for}}</h2>
    <p>Description: {{post.description}}</p>
    <p>Location: {{post.location}}</p>
    <h3>Tags:</h3>
    <div v-for="tag in post.tags">
      <p>{{tag.name}}</p>
    </div>
    <p>Created {{relativeDate(post.created_at)}}</p>
    <div v-if="$parent.getUserId() == post.user_id">
      <router-link :to="`/posts/${post.id}/edit`">Edit</router-link>
    </div> 
      

<!-- offers index -->
    <h2>Offers</h2>
    <div v-for="offer in post.offers">
      <router-link :to="`/users/${offer.user_id}`">
        <h3>{{offer.user.first_name}} {{offer.user.last_name}}</h3>
      </router-link>
      <p>Offer Accepted Status: {{offer.accepted}}</p>
      <div v-if="offer.accepted === true">
        <h3>This offer has been accepted</h3> 
      </div>
      <img :src="offer.image_url" alt="" class="image-fit">
      <p>Message: {{offer.message}}</p>
      <p>Created {{relativeDate(offer.created_at)}}</p>


<!-- accept and edit offer buttons-->
      <div v-if="$parent.getUserId() == post.user_id">
        <div v-if="!post.offer_accepted">
          <button v-on:click="updateOfferAccept(offer)">
            Accept Offer
          </button>
        </div>
        <div v-if="offer.accepted === true">
          <p>{{offer.user.first_name}} {{offer.user.last_name}}'s email: {{offer.user.email}}</p>
        </div>
      </div>
      <div v-if="$parent.getUserId() == offer.user_id">
        <button v-on:click="offerEditAuthentication = offer.id">
          Edit
        </button>


<!-- update offer -->
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

<!-- new offer -->
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
        <input type="file" class="form-control" v-on:change="setFile($event)" ref="fileInput">
      </div>
      <input type="submit" class="btn btn-primary" value="Submit">
    </form>

  </div>
</template>

<style></style>

<script>
import axios from "axios";
import moment from "moment";

export default {
  data: function() {
    return {
      post: {
        user: {}
      },
      errors: [],
      offerEditAuthentication: null,
      message: "",
      image: ""
    };
  },
  created: function() {
    this.showPost();
  },
  methods: {
    updateOfferAccept: function(offer) {
      var formData = new FormData();
      formData.append("accepted", true);
      axios.patch(`/api/offers/${offer.id}`, formData).then(response => {
        console.log(response.data);
        offer.accepted = true;
        this.post.offer_accepted = true;
      });
    },
    relativeDate: function (date) {
      return moment(date).fromNow();
    },
    setFile: function(event) {
      if (event.target.files.length > 0) {
        this.image = event.target.files[0];
      }
    },
    showPost: function () {
      axios.get(`/api/posts/${this.$route.params.id}`).then(response => {
        console.log(response.data);
        this.post = response.data;
      });
    },
    destroyPost: function () {
      axios.delete(`/api/posts/${this.post.id}`).then(response => {
        console.log("Success", response.data);
        this.$router.push("/posts");
      });
    },
    createOffer: function() {
      var formData = new FormData();
      formData.append("message", this.message);
      formData.append("post_id", this.post.id);
      if (this.image) {
        formData.append("image", this.image);
      }
      axios
        .post("/api/offers", formData)
        .then((response) => {
          this.post.offers.push(response.data);
          this.message = "";
          this.image = "";
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
    updateOffer: function (offer) {
      var formData = new FormData();
      var index = this.post.offers.indexOf(offer);
      formData.append("message", offer.message);
      formData.append("post_id", offer.post_id);
      if (this.image) {
        formData.append("image", this.image);
      }
      axios
        .patch(`/api/offers/${offer.id}`, formData)
        .then((response) => {
          this.post.offers[index] = (response.data);
          this.offerEditAuthentication = null;
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
    destroyOffer: function (offer) {
      var index = this.post.offers.indexOf(offer);
      axios.delete(`/api/offers/${offer.id}`).then(response => {
        console.log("Success", response.data);
        this.post.offers.splice(index, 1);
      });
    },
  },
};
</script>
