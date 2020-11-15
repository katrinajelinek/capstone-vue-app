<template>
  <div class="posts-index">
    <!-- search -->
    <!-- <input
      type="text"
      v-model="plantTypeFilter"
      placeholder="Search by Clipping"
      list="plantTypes"
    />
    <br />
    <datalist id="plantTypes">
      <option v-for="post in posts">{{ post.plant_type }}</option>
    </datalist> -->

    <!-- filter by tags multiselect -->
    <!-- <div>
      <label class="typo__label">Or search by tags:</label>
      <multiselect
        v-model="tagsFilter"
        :options="tags"
        :multiple="true"
        :close-on-select="false"
        :clear-on-select="false"
        :preserve-search="true"
        placeholder="Select Tags"
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

    <!-- Portfolio Section -->
    <div id="portfolio">
      <div class="container padding-top padding-bottom">
        <div class="row section-title text-center">
          <div class="col-sm-8 col-sm-offset-2">
            <h1><span>Recent</span> Portfolio</h1>
            <p>
              Lorem ipsum dolor sit amet, consectetur adipiscing elit. Maecenas
              ac augue at erat hendrerit dictum. Praesent porta, purus eget
              sagittis imperdiet.
            </p>
          </div>
        </div>
        <div class="row portfolio-wrapper">
          <div
            v-if="post.offer_accepted == null"
            class="col-sm-5 portfolio-content"
            v-for="post in orderBy(
              filterBy(filteredPostsByTags, plantTypeFilter, 'plant_type'),
              sortAttribute,
              -1
            )"
          >
            <div class="padding-top padding-bottom padding-left">
              <div class="single-project big-project">
                <div class="portfolio-item">
                  <img class="img-responsive" :src="post.image_url" alt="" />
                  <div class="overlay">
                    <div class="overlay-content">
                      <!-- <span
                        ><a href="portfolio-details.html"
                          ><i class="fa fa-arrow-circle-o-right"></i></a
                      ></span> -->
                      <span
                        ><router-link :to="`/posts/${post.id}`">
                          <h2>{{ post.plant_type }}</h2>
                        </router-link>
                        <h3>Clipped by:</h3>
                        <p>
                          {{ post.user.first_name }} {{ post.user.last_name }}
                        </p>
                        <p>Location: {{ post.location }}</p>
                        <p>Tags:</p>
                        <div v-for="tag in post.tags">
                          {{ tag.name }}
                        </div>
                        <p>Created {{ relativeDate(post.created_at) }}</p>
                      </span>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- #/ Portfolio Section -->

    <!-- all posts -->
    <!-- <div
      v-for="post in orderBy(
        filterBy(filteredPostsByTags, plantTypeFilter, 'plant_type'),
        sortAttribute,
        -1
      )"
    > -->
    <!-- <div v-if="post.offer_accepted == null">
        <img src="post.image_url" alt="" class="image-fit" />
        <router-link :to="`/posts/${post.id}`">
          <h2>{{ post.plant_type }}</h2>
        </router-link>
        <h3>Clipped by:</h3>
        <router-link :to="`/users/${post.user_id}`">
          {{ post.user.first_name }} {{ post.user.last_name }}
        </router-link>
        <p>Location: {{ post.location }}</p>
        <p>Tags:</p>
        <div v-for="tag in post.tags">
          {{ tag.name }}
        </div>
        <p>Created {{ relativeDate(post.created_at) }}</p>
        <br />
        <br />
      </div> -->
  </div>
</template>

<style></style>

<script>
import axios from "axios";
import Multiselect from "vue-multiselect";
import Vue2Filters from "vue2-filters";
import moment from "moment";

export default {
  mixins: [Vue2Filters.mixin],
  components: {
    // Multiselect,
  },
  data: function() {
    return {
      posts: [],
      sortAttribute: "created_at",
      values: [],
      tags: [],
      tagsFilter: [],
      plantTypeFilter: "",
    };
  },
  created: function() {
    this.indexPosts();
    this.indexTags();
  },
  computed: {
    filteredPostsByTags() {
      return this.getByTag(this.posts, this.tagsFilter);
    },
  },
  methods: {
    relativeDate: function(date) {
      return moment(date).fromNow();
    },
    indexPosts: function() {
      axios.get("/api/posts").then((response) => {
        console.log(response.data);
        this.posts = response.data;
      });
    },
    setSortAttribute: function(attribute) {
      this.sortAttribute = attribute;
    },
    indexTags: function() {
      axios.get("/api/tags").then((response) => {
        console.log(response.data);
        this.tags = response.data;
      });
    },
    getByTag: function(posts, tagsFilter) {
      tagsFilter.forEach((tag) => {
        posts = this.filterBy(posts, tag.name);
      });
      return posts;
    },
  },
};
</script>
