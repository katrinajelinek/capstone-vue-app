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
        <input type="file" class="form-control" v-on:change="setFile($event)" ref="fileInput">
      </div>
      <input type="submit" class="btn btn-primary" value="Submit">
    </form>

    <div>
      <label class="typo__label">Select Tags</label>
      <multiselect v-model="values" :options="tags" :multiple="true" :close-on-select="false" :clear-on-select="false" :preserve-search="true" placeholder="Pick some" label="name" track-by="name" :preselect-first="true">
        <template slot="selection" slot-scope="{ values, isOpen }"><span class="multiselect__single" v-if="values.length &amp;&amp; !isOpen">{{ values.length }} tags selected</span></template>
      </multiselect>
      <!-- <pre class="language-json"><code>{{ value  }}</code></pre> -->
    </div>
    {{values}}
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
      image: "",
      errors: [],
      values: [],
      tags: [],
    };
  },
  created: function() {
    this.indexTags();
  },
  methods: {
    setFile: function(event) {
      if (event.target.files.length > 0) {
        this.image = event.target.files[0];
      }
    },
    createPost: function() {
      var tagIds = this.values.map(tag => {
        return tag.id;
      });
      var formData = new FormData();
      formData.append("plant_type", this.plantType);
      formData.append("trade_for", this.tradeFor);
      formData.append("description", this.description);
      formData.append("location", this.location);
      formData.append("image_url", this.image);
      formData.append("tag_ids", tagIds);
      axios
        .post("/api/posts", formData)
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