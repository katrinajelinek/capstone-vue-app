<template>
  <div class="posts-edit">
    <div class="users-edit">
      <div class="replay-box">
        <div class="row">
          <div class="col-sm-12">
            <div class="container">
              <h3>Edit your clipping</h3>
              <ul>
                <li class="text-danger" v-for="error in errors">{{ error }}</li>
              </ul>
              <form
                id="comment-form"
                class="row"
                name="comment-form"
                method="post"
                v-on:submit.prevent="createPost()"
              >
                <div class="col-sm-6">
                  <div class="form-group">
                    <input
                      type="text"
                      name="name"
                      class="form-control"
                      required="required"
                      placeholder="Plant Type"
                      v-model="post.plant_type"
                    />
                  </div>
                </div>
                <div class="col-sm-6">
                  <div class="form-group">
                    <input
                      type="text"
                      name="name"
                      class="form-control"
                      required="required"
                      placeholder="Trade for"
                      v-model="post.trade_for"
                    />
                  </div>
                </div>
                <div class="col-sm-6">
                  <div class="form-group">
                    <input
                      type="text"
                      name="name"
                      class="form-control"
                      required="required"
                      placeholder="Description"
                      v-model="post.description"
                    />
                  </div>
                </div>
                <div class="col-sm-6">
                  <div class="form-group">
                    <input
                      type="text"
                      name="name"
                      class="form-control"
                      required="required"
                      placeholder="Your location"
                      v-model="post.location"
                    />
                  </div>
                </div>
                <div class="col-sm-6">
                  <div class="form-group">
                    <div>
                      <multiselect
                        v-model="values"
                        :options="tags"
                        :multiple="true"
                        :close-on-select="false"
                        :clear-on-select="false"
                        :preserve-search="true"
                        placeholder="Tags"
                        label="name"
                        track-by="name"
                        :preselect-first="true"
                      >
                        <template
                          slot="selection"
                          slot-scope="{ values, isOpen }"
                          ><span
                            class="multiselect__single"
                            v-if="values.length &amp;&amp; !isOpen"
                            >{{ values.length }} tags selected</span
                          ></template
                        >
                      </multiselect>
                      <!-- <pre class="language-json"><code>{{ value  }}</code></pre> -->
                    </div>
                  </div>
                </div>
                <div class="col-sm-6">
                  <div class="form-group">
                    <input
                      type="file"
                      name="name"
                      class="form-control"
                      required="required"
                      v-on:change="setFile($event)"
                      ref="fileInput"
                    />
                  </div>
                </div>
                <div class="col-sm-12 form-group">
                  <button
                    class="btn btn-primary pull-right"
                    v-on:click="destroyPost()"
                  >
                    Delete
                  </button>
                  <button type="submit" class="btn btn-primary pull-right">
                    Edit
                  </button>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- <form v-on:submit.prevent="updatePost(post)">
      <h1>Edit Clipping</h1>
      <ul>
        <li class="text-danger" v-for="error in errors">{{ error }}</li>
      </ul>
      <div class="form-group">
        <label>Plant type:</label>
        <input type="text" class="form-control" v-model="post.plant_type" />
      </div>
      <div class="form-group">
        <label>Trade for:</label>
        <input type="text" class="form-control" v-model="post.trade_for" />
      </div>
      <div class="form-group">
        <label>Description</label>
        <input type="text" class="form-control" v-model="post.description" />
      </div>
      <div class="form-group">
        <label>Location:</label>
        <input type="text" class="form-control" v-model="post.location" />
      </div>
      <div class="form-group">
        <label>Image:</label>
        <input
          type="file"
          class="form-control"
          v-on:change="setFile($event)"
          ref="fileInput"
        />
      </div>
      <input type="submit" class="btn btn-primary" value="Update" />
    </form>
    <button v-on:click="destroyPost()">
      Delete
    </button> -->

    <!-- tags multiselect -->
    <!-- <div>
      <label class="typo__label">Select Tags</label>
      <multiselect
        v-model="values"
        :options="tags"
        :multiple="true"
        :close-on-select="false"
        :clear-on-select="false"
        :preserve-search="true"
        placeholder="Pick some"
        label="name"
        track-by="name"
        :preselect-first="true"
      >
        <template slot="selection" slot-scope="{ values, isOpen }"
          ><span
            class="multiselect__single"
            v-if="values.length &amp;&amp; !isOpen"
            >{{ values.length }} tags selected</span
          ></template
        >
      </multiselect>
    </div> -->
  </div>
</template>

<style scoped>
.contact-wrap .btn.btn-primary,
#comment-form .btn.btn-primary {
  margin-left: 15px;
}
</style>

<script>
import axios from "axios";
import Multiselect from "vue-multiselect";

export default {
  components: {
    Multiselect,
  },
  data: function() {
    return {
      post: {},
      errors: [],
      tags: [],
      values: [],
      image: "",
    };
  },
  created: function() {
    this.showPost();
    this.indexTags();
  },
  methods: {
    setFile: function(event) {
      if (event.target.files.length > 0) {
        this.image = event.target.files[0];
      }
    },
    showPost: function() {
      axios.get(`/api/posts/${this.$route.params.id}`).then((response) => {
        console.log(response.data);
        this.post = response.data;
        this.values = response.data.tags;
      });
    },
    updatePost: function(post) {
      var tagIds = this.values.map((tag) => {
        return tag.id;
      });
      var formData = new FormData();
      formData.append("plant_type", post.plant_type);
      formData.append("trade_for", post.trade_for);
      formData.append("description", post.description);
      formData.append("location", post.location);
      if (this.image) {
        formData.append("image", this.image);
      }
      formData.append("tag_ids", JSON.stringify(tagIds));
      axios
        .patch(`/api/posts/${this.post.id}`, formData)
        .then((response) => {
          this.$router.push(`/posts/${this.post.id}`);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    destroyPost: function() {
      axios.delete(`/api/posts/${this.post.id}`).then((response) => {
        console.log("Success", response.data);
        this.$router.push("/posts");
      });
    },
    indexTags: function() {
      axios.get("/api/tags").then((response) => {
        console.log(response.data);
        this.tags = response.data;
      });
    },
  },
};
</script>
