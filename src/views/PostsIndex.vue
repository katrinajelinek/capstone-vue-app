<template>
  <div class="posts-index">
    <!-- filter by tags multiselect -->
    <div class="col-sm-8 col-sm-offset-2">
      <div>
        <multiselect
          v-model="tagsFilter"
          :options="tags"
          :multiple="true"
          :close-on-select="false"
          :clear-on-select="false"
          :preserve-search="true"
          placeholder="Search by Tags"
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
      </div>
    </div>

    <!-- Portfolio Section -->
    <div id="portfolio">
      <div class="container padding-top padding-bottom">
        <div class="row section-title text-center">
          <div class="col-sm-8 col-sm-offset-2">
            <h1><span>Welcome to</span> Clipsy</h1>
            <p>
              Where plant friends can trade clippings and seeds with their
              community
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
                  <div v-if="post.image_url">
                    <img class="img-responsive" :src="post.image_url" alt="" />
                  </div>
                  <div v-else>
                    <img
                      class="img-responsive"
                      src="images/default-post-picture.jpg"
                      alt=""
                    />
                  </div>
                  <div class="overlay">
                    <div class="overlay-content">
                      <router-link :to="`/posts/${post.id}`">
                        <h2>{{ post.plant_type }}</h2>
                      </router-link>
                      <h4>
                        Clipped by:
                        <router-link :to="`/users/${post.user_id}`">
                          {{ post.user.first_name }} {{ post.user.last_name }}
                        </router-link>
                      </h4>
                      <br />
                      <h5>Location: {{ post.location }}</h5>
                      <br />
                      <i class="fa fa-tags"></i>
                      <p v-for="tag in post.tags">{{ tag.name }},</p>
                      <br />
                      <p class="fa fa-calendar">
                        Created {{ relativeDate(post.created_at) }}
                      </p>
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

<style scoped>
.portfolio-item .overlay .overlay-content {
  margin-top: -200px;
}
.col-sm-5 {
  width: 50%;
}
</style>

<script>
import axios from "axios";
import Multiselect from "vue-multiselect";
import Vue2Filters from "vue2-filters";
import moment from "moment";

export default {
  mixins: [Vue2Filters.mixin],
  components: {
    Multiselect,
  },
  data: function() {
    return {
      posts: [],
      sortAttribute: "created_at",
      values: [],
      tags: [],
      tagsFilter: [],
      plantTypeFilter: "",
      default: "/public/images/default-post-picture.jpg",
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
