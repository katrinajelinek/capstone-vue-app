<template>
  <div class="posts-index">

    <router-link :to="`/posts/new`">
      Post a clipping
    </router-link>

    <div v-for="post in posts">
      <img :src="post.image_url" alt="">
      <router-link :to="`/posts/${post.id}`">
        <h2>{{post.plant_type}}</h2>
      </router-link>
      <h3>Clipped by: {{post.user.first_name}} {{post.user.last_name}}</h3>
      <p>Trade for: {{post.trade_for}}</p>
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
      sortAttribute: "created_at",
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
    },
    setSortAttribute: function (attribute) {
      this.sortAttribute = attribute;
    },
  },
};
</script>
