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
    <div>
      <label class="typo__label">Select Tags</label>
      <multiselect v-model="values" :options="tags" :multiple="true" :close-on-select="false" :clear-on-select="false" :preserve-search="true" placeholder="Pick some" label="name" track-by="name" :preselect-first="true">
        <template slot="selection" slot-scope="{ values, search, isOpen }"><span class="multiselect__single" v-if="values.length &amp;&amp; !isOpen">{{ values.length }} tags selected</span></template>
      </multiselect>
      <!-- <pre class="language-json"><code>{{ value  }}</code></pre> -->
    </div>
    {{tags}}
  </div>
</template>

<script>
import axios from "axios";
import Multiselect from "vue-multiselect";

export default {
  components: {
    Multiselect
  },
  data: function() {
    return {
      plantType: "",
      tradeFor: "",
      description: "",
      location: "",
      imageUrl: "",
      post: {},
      errors: [],
      tags: [],
      values: []
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
        plant_type: this.post.plantType,
        trade_for: this.post.tradeFor,
        description: this.post.description,
        location: this.post.location,
        image_url: this.post.imageUrl
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
    destroyPost: function () {
      axios.delete(`/api/posts/${this.post.id}`).then(response => {
        console.log("Success", response.data);
        this.$router.push("/posts");
      });
    },
    indexTags: function () {
      axios.get("/api/tags").then(response => {
        console.log(response.data);
        this.tags = response.data;
      });
    },
  }
};
</script>