<template>
  <div class="posts-index">
    <div>
      <label class="typo__label">Select Tags</label>
      <multiselect v-model="values" :options="tags" :multiple="true" :close-on-select="false" :clear-on-select="false" :preserve-search="true" placeholder="Pick some" label="name" track-by="name" :preselect-first="true">
        <template slot="selection" slot-scope="{ values, isOpen }"><span class="multiselect__single" v-if="values.length &amp;&amp; !isOpen">{{ values.length }} tags selected</span></template>
      </multiselect>
      <!-- <pre class="language-json"><code>{{ value  }}</code></pre> -->
    </div>

    <router-link :to="`/posts/new`">
      Post a clipping
    </router-link>

    <div v-for="post in posts">
      <img :src="post.image_url" alt="">
      <router-link :to="`/posts/${post.id}`">
        <h2>{{post.plant_type}}</h2>
      </router-link>
      <h3>Clipped by:</h3>
      <router-link :to="`/users/${post.user_id}`">
        {{post.user.first_name}} {{post.user.last_name}}
      </router-link>
      <p>Trade for: {{post.trade_for}}</p>
      <p>Description: {{post.description}}</p>
      <p>Location: {{post.location}}</p>
      <p>Tags:</p>
      <div v-for="tag in post.tags">
        {{tag.name}}
      </div>
      <p>Created at: {{post.created_at}}</p> <br> <br>
    </div>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
import Multiselect from "vue-multiselect";

export default {
  components: {
    Multiselect
  },
  data: function() {
    return {
      posts: [],
      sortAttribute: "created_at",
      values: [],
      tags: [],
    };
  },
  created: function() {
    this.indexPosts();
    this.indexTags();
  },
  methods: {
    indexPosts: function () {
      axios.get("/api/posts").then(response => {
        console.log(response.data);
        this.posts = response.data;
      });
    },
    setSortAttribute: function (attribute) {
      this.sortAttribute = attribute;
    },
    indexTags: function () {
      axios.get("/api/tags").then(response => {
        console.log(response.data);
        this.tags = response.data;
      });
    },
  },
};
</script>
