<template>
  <div id="sub-page" class="users-show">
    <!-- About -->
    <div id="about">
      <div class="container padding-top">
        <div class="row section-title text-center">
          <div class="col-sm-8 col-sm-offset-2">
            <h1>My Profile</h1>
          </div>
        </div>
        <div class="row padding-bottom">
          <div class="col-sm-6">
            <img class="img-responsive" :src="user.image_url" alt="About" />
          </div>
          <div class="col-sm-6">
            <h2>{{ user.first_name }} {{ user.last_name }}</h2>
            <!-- <p><i class="fa fa-envelope"></i> {{ user.email }}</p> -->
            <div v-if="$parent.getUserId() == user.id">
              <router-link
                class="btn btn-primary"
                :to="`/users/${user.id}/edit`"
                >Edit Profile</router-link
              >

              <br />
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- #/ About -->

    <div id="our-team">
      <!-- our-team -->
      <div class="container text-center our-team padding-top padding-bottom">
        <div class="row section-title text-center">
          <div class="col-sm-8 col-sm-offset-2">
            <h1>My Clippings</h1>
          </div>
        </div>

        <div id="content" class="site-content col-md-12">
          <div
            class="col-md-4 col-sm-6"
            v-for="post in orderBy(user.posts, 'created_at', -1)"
          >
            <div class="post">
              <div class="entry-header">
                <div class="entry-thumbnail">
                  <img class="img-responsive" :src="post.image_url" alt="" />
                </div>
              </div>
              <div v-if="post.offer_accepted">
                <h4>An offer has been accepted</h4>
              </div>
              <div class="post-content">
                <h2 class="entry-title">
                  <router-link :to="`/posts/${post.id}`">{{
                    post.plant_type
                  }}</router-link>
                </h2>
                <div class="entry-meta">
                  <ul>
                    <li class="publish-date">
                      <i class="fa fa-calendar"></i
                      ><a href="#"
                        >Created {{ relativeDate(post.created_at) }}</a
                      >
                    </li>
                    <li class="tag">
                      <i class="fa fa-tags"></i>
                      <a v-for="tag in post.tags">{{ tag.name }}, </a>
                    </li>
                  </ul>
                </div>
                <div class="entry-summary">
                  <p>
                    {{ post.description }}
                  </p>
                </div>
              </div>
            </div>
            <!--/post-->
          </div>
        </div>
      </div>
      <!-- #/ our-team -->
    </div>
    <div id="our-team">
      <!-- our-team -->
      <div class="container text-center our-team padding-bottom">
        <div class="row section-title text-center">
          <div class="col-sm-8 col-sm-offset-2">
            <h1>My Offers</h1>
          </div>
        </div>

        <div id="content" class="site-content col-md-12">
          <div
            class="col-md-4 col-sm-6"
            v-for="offer in orderBy(user.offers, 'created_at', -1)"
          >
            <div class="post">
              <div class="entry-header">
                <div class="entry-thumbnail">
                  <img class="img-responsive" :src="offer.image_url" alt="" />
                </div>
              </div>
              <div class="post-content">
                <h2 class="entry-title">
                  <router-link :to="`/posts/${offer.post_id}`">
                    {{ offer.post_title }}</router-link
                  >
                </h2>
                <div class="entry-meta">
                  <ul>
                    <li class="publish-date">
                      <i class="fa fa-calendar"></i
                      ><a href="#"
                        >Created {{ relativeDate(offer.created_at) }}</a
                      >
                    </li>
                  </ul>
                </div>
                <div class="entry-summary">
                  <p>
                    {{ offer.message }}
                  </p>
                </div>
              </div>
            </div>
            <!--/post-->
          </div>
        </div>
      </div>
      <!-- #/ our-team -->
    </div>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
import Vue2Filters from "vue2-filters";
import moment from "moment";

export default {
  mixins: [Vue2Filters.mixin],
  data: function() {
    return {
      user: {},
      errors: [],
      offerEditAuthentication: null,
      sortAttribute: "created_at",
    };
  },
  created: function() {
    this.showUser();
  },
  methods: {
    relativeDate: function(date) {
      return moment(date).fromNow();
    },
    setFile: function(event) {
      if (event.target.files.length > 0) {
        this.image = event.target.files[0];
      }
    },
    showUser: function() {
      axios.get(`/api/users/${this.$route.params.id}`).then((response) => {
        console.log(response.data);
        this.user = response.data;
      });
    },
    updateOffer: function(offer) {
      var formData = new FormData();
      var index = this.user.offers.indexOf(offer);
      formData.append("message", offer.message);
      formData.append("post_id", offer.post_id);
      if (this.image) {
        formData.append("image", this.image);
      }
      axios
        .patch(`/api/offers/${offer.id}`, formData)
        .then((response) => {
          this.user.offers[index] = response.data;
          this.offerEditAuthentication = null;
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    setSortAttribute: function(attribute) {
      this.sortAttribute = attribute;
    },
  },
};
</script>
