<template>
  <div class="posts-edit">
    <form v-on:submit.prevent="updatePost()">
      <h1>Edit Clipping</h1>
      <ul>
        <li class="text-danger" v-for="error in errors">{{ error }}</li>
      </ul>
      <div class="form-group">
        <label>Plant type:</label> 
        <input type="text" class="form-control" v-model="post.plant_type">
      </div>
      <div class="form-group">
        <label>Trade for:</label> 
        <input type="text" class="form-control" v-model="post.trade_for">
      </div>
      <div class="form-group">
        <label>Description</label> 
        <input type="text" class="form-control" v-model="post.description">
      </div>
      <div class="form-group">
        <label>Location:</label> 
        <input type="text" class="form-control" v-model="post.location">
      </div>
      <div class="form-group">
        <label>Image:</label> 
        <input type="text" class="form-control" v-model="post.image_url">
      </div>
      <input type="submit" class="btn btn-primary" value="Update">
    </form>
    <button v-on:click="destroyPost()">
      Delete
    </button>
    <div v-for="tag in tags">
      <p>{{tag.name}}</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      post: {},
      errors: [],
      tags: []
    };
  },
  created: function() {
    axios.get(`/api/posts/${this.$route.params.id}`).then(response => {
      console.log(response.data);
      this.post = response.data;
    });
  },
  methods: {
    updatePost: function() {
      var params = {
        plant_type: this.post.plant_type,
        trade_for: this.post.trade_for,
        description: this.post.description,
        location: this.post.location,
        image_url: this.post.image_url
      };
      axios
        .patch(`/api/posts/${this.post.id}`, params)
        .then((response) => {
          this.$router.push(`/posts/${this.post.id}`);
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
    indexTags: function () {
      axios.get("/api/tags").then(response => {
        console.log(response.data);
        this.tags = response.data;
      });
    },
    destroyPost: function () {
      axios.delete(`/api/posts/${this.post.id}`).then(response => {
        console.log("Success", response.data);
        this.$router.push("/posts");
      });
    }
  }
};
</script>