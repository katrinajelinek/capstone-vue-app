<template>
  <div class="posts-new">
    <form v-on:submit.prevent="createPost()">
      <h1>New Clipping</h1>
      <ul>
        <li class="text-danger" v-for="error in errors">{{ error }}</li>
      </ul>
      <div class="form-group">
        <label>Plant type:</label> 
        <input type="text" class="form-control" v-model="plantType">
      </div>
      <div class="form-group">
        <label>Trade for:</label> 
        <input type="text" class="form-control" v-model="tradeFor">
      </div>
      <div class="form-group">
        <label>Description</label> 
        <input type="text" class="form-control" v-model="description">
      </div>
      <div class="form-group">
        <label>Location:</label> 
        <input type="text" class="form-control" v-model="location">
      </div>
      <div class="form-group">
        <label>Image:</label> 
        <input type="text" class="form-control" v-model="imageUrl">
      </div>
      <input type="submit" class="btn btn-primary" value="Submit">
    </form>

    <div>
      <label class="typo__label">Select Tags</label>
      <multiselect v-model="values" :options="tags" :multiple="true" :close-on-select="false" :clear-on-select="false" :preserve-search="true" placeholder="Pick some" label="name" track-by="name" :preselect-first="true">
        <template slot="selection" slot-scope="{ values, search, isOpen }"><span class="multiselect__single" v-if="values.length &amp;&amp; !isOpen">{{ values.length }} tags selected</span></template>
      </multiselect>
      <!-- <pre class="language-json"><code>{{ value  }}</code></pre> -->
    </div>
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
      errors: [],
      values: [],
      tags: [],
      tagIds: []
    };
  },
  created: function() {
    this.indexTags();
  },
  methods: {
    createPost: function() {
      var params = {
        plant_type: this.plantType,
        trade_for: this.tradeFor,
        description: this.description,
        location: this.location,
        image_url: this.imageUrl,
        tag_ids: this.tagIds,
      };
      axios
        .post("/api/posts", params)
        .then((response) => {
          this.$router.push("/posts");
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
  }
};
</script>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>