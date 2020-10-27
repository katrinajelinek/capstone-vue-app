<template>
  <div class="posts-index">
    <div v-for="post in posts">
      <h2>{{post.plant_type}}</h2>
      <router-link :to="`/posts/${post.id}`">
        <img :src="post.image_url" alt="">
      </router-link>
      <h3>Trade for: {{post.trade_for}}</h3>
      <p>Description: {{post.description}}</p>
      <p>Loaction: {{post.location}}</p>
      <p>Created at: {{post.created_at}}</p> <br> <br>
    </div>
  </div>
</template>

<style></style>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      posts: [],
    };
  },
  created: function() {
    this.postsIndex();
  },
  methods: {
    postsIndex: function () {
      axios.get("/api/posts").then(response => {
        console.log(response.data);
        this.posts = response.data;
      });
    }
  },
};
</script>
