<template>
  <div id="sub-page" class="posts-show">
    <div id="about">
      <div class="container padding-top">
        <div class="row section-title text-center">
          <div class="col-sm-8 col-sm-offset-2">
            <h1>{{ post.plant_type }}</h1>
            <h4>
              Clipped by:
              <router-link :to="`/users/${post.user_id}`">
                {{ post.user.first_name }} {{ post.user.last_name }}
              </router-link>
            </h4>
            <div v-if="post.offer_accepted">
              <h5>An offer has been accepted</h5>
            </div>
            <div v-if="$parent.getUserId() == post.user_id">
              <router-link
                class="btn btn-primary"
                :to="`/posts/${post.id}/edit`"
                >Edit Your Clipping</router-link
              >
            </div>
          </div>
        </div>
        <div class="row padding-bottom">
          <div class="col-sm-6">
            <img class="img-responsive" :src="post.image_url" alt="About" />
          </div>
          <div class="col-sm-6 npl">
            <h5>Trade for: {{ post.trade_for }}</h5>
            <br />
            <h5>Description: {{ post.description }}</h5>
            <br />
            <h5>Location: {{ post.location }}</h5>
            <br />
            <h5>Tags:</h5>
            <div v-for="tag in post.tags">
              <h6>{{ tag.name }}</h6>
            </div>
            <br />
            <p>Created {{ relativeDate(post.created_at) }}</p>
          </div>
        </div>
      </div>
    </div>
    <div id="our-team">
      <!-- our-team -->
      <div class="container text-center our-team padding-bottom">
        <div class="row section-title text-center">
          <div class="col-sm-8 col-sm-offset-2">
            <h1>Offers on this Clipping</h1>
          </div>
        </div>
        <div id="content" class="site-content col-md-12">
          <div
            class="col-md-4 col-sm-4"
            v-for="offer in orderBy(post.offers, 'created_at', -1)"
          >
            <div class="post">
              <div v-if="offer.accepted === true">
                <h4>Accepted</h4>
              </div>
              <div class="entry-header">
                <div class="entry-thumbnail">
                  <img class="img-responsive" :src="offer.image_url" alt="" />
                </div>
              </div>
              <div class="post-content">
                <h2 class="entry-title">
                  <a href="blog-detail.html">{{ offer.post_title }}</a>
                </h2>
                <div class="entry-meta">
                  <ul>
                    <li class="author">
                      <i class="fa fa-user"></i
                      ><router-link :to="`/users/${offer.user_id}`">
                        {{ offer.user.first_name }} {{ offer.user.last_name }}
                      </router-link>
                    </li>
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
              <div v-if="$parent.getUserId() == post.user_id">
                <div v-if="!post.offer_accepted">
                  <button
                    v-on:click="updateOfferAccept(offer)"
                    class="btn btn-primary"
                  >
                    Accept Offer
                  </button>
                </div>
                <div v-if="offer.accepted === true">
                  <p>
                    {{ offer.user.first_name }}
                    {{ offer.user.last_name }}'s email:
                    {{ offer.user.email }}
                  </p>
                </div>
              </div>
              <div v-if="$parent.getUserId() == offer.user_id">
                <button
                  class="btn btn-primary"
                  v-on:click="offerEditAuthentication = offer.id"
                >
                  Edit Offer
                </button>
              </div>
            </div>
            <div id="our-team">
              <!-- our-team -->
              <div v-if="offerEditAuthentication === offer.id">
                <div
                  class="container text-center our-team padding-top padding-bottom"
                >
                  <div class="row section-title text-center">
                    <div class="col-sm-8 col-sm-offset-2">
                      <h1>Edit your clipping</h1>
                    </div>
                  </div>
                  <ul>
                    <li class="text-danger" v-for="error in errors">
                      {{ error }}
                    </li>
                  </ul>
                  <form
                    v-on:submit.prevent="updateOffer(offer)"
                    id="comment-form"
                    class="row"
                    name="comment-form"
                    method="post"
                  >
                    <div class="col-sm-12">
                      <div class="form-group">
                        <input
                          name="message"
                          id="message"
                          required="required"
                          class="form-control"
                          rows="7"
                          placeholder="Your Message"
                          v-model="offer.message"
                        />
                      </div>
                    </div>
                    <div class="col-sm-12">
                      <div class="form-group">
                        <input
                          type="file"
                          name="name"
                          class="form-control"
                          v-on:change="setFile($event)"
                          ref="fileInput"
                        />
                      </div>
                    </div>
                    <div class="col-sm-12 form-group">
                      <button type="submit" class="btn btn-primary pull-right">
                        Update Offer
                      </button>
                      <button
                        class="btn btn-primary pull-right"
                        v-on:click="destroyOffer(offer)"
                      >
                        Delete
                      </button>
                    </div>
                  </form>
                </div>
              </div>

              <!-- #/ our-team -->
            </div>
            <!--/post-->
          </div>
        </div>

        <!--/offers-area-->
        <div id="our-team">
          <!-- our-team -->
          <div
            class="container text-center our-team padding-top padding-bottom"
          >
            <div class="row section-title text-center">
              <div class="col-sm-8 col-sm-offset-2">
                <h1>Place an Offer</h1>
              </div>
            </div>
            <ul>
              <li class="text-danger" v-for="error in errors">{{ error }}</li>
            </ul>
            <form
              v-on:submit.prevent="createOffer()"
              id="comment-form"
              class="row"
              name="comment-form"
              method="post"
            >
              <div class="col-sm-12">
                <div class="form-group">
                  <input
                    name="message"
                    id="message"
                    required="required"
                    class="form-control"
                    rows="7"
                    placeholder="Your Message"
                    v-model="message"
                  />
                </div>
              </div>
              <div class="col-sm-12">
                <div class="form-group">
                  <input
                    type="file"
                    name="name"
                    class="form-control"
                    v-on:change="setFile($event)"
                    ref="fileInput"
                  />
                </div>
              </div>
              <div class="col-sm-12 form-group">
                <button type="submit" class="btn btn-primary pull-right">
                  Place Offer
                </button>
              </div>
            </form>
          </div>
          <!-- #/ our-team -->
        </div>
      </div>
      <!-- #/ our-team -->
    </div>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
import moment from "moment";
import Vue2Filters from "vue2-filters";

export default {
  mixins: [Vue2Filters.mixin],
  data: function() {
    return {
      post: {
        user: {},
      },
      errors: [],
      offerEditAuthentication: null,
      message: "",
      image: "",
      flashMessage: "",
    };
  },
  created: function() {
    this.showPost();
  },
  methods: {
    updateOfferAccept: function(offer) {
      var formData = new FormData();
      formData.append("accepted", true);
      axios.patch(`/api/offers/${offer.id}`, formData).then((response) => {
        console.log(response.data);
        offer.accepted = true;
        this.post.offer_accepted = true;
      });
    },
    relativeDate: function(date) {
      return moment(date).fromNow();
    },
    setFile: function(event) {
      if (event.target.files.length > 0) {
        this.image = event.target.files[0];
      }
    },
    showPost: function() {
      axios.get(`/api/posts/${this.$route.params.id}`).then((response) => {
        console.log(response.data);
        this.post = response.data;
      });
    },
    destroyPost: function() {
      axios.delete(`/api/posts/${this.post.id}`).then((response) => {
        console.log("Success", response.data);
        this.$router.push("/");
      });
    },
    createOffer: function() {
      var formData = new FormData();
      formData.append("message", this.message);
      formData.append("post_id", this.post.id);
      if (this.image) {
        formData.append("image", this.image);
      }
      axios
        .post("/api/offers", formData)
        .then((response) => {
          this.post.offers.push(response.data);
          this.message = "";
          document.getElementById("imageInput").value = "";
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    updateOffer: function(offer) {
      var formData = new FormData();
      var index = this.post.offers.indexOf(offer);
      formData.append("message", offer.message);
      formData.append("post_id", offer.post_id);
      if (this.image) {
        formData.append("image", this.image);
      }
      axios
        .patch(`/api/offers/${offer.id}`, formData)
        .then((response) => {
          this.post.offers[index] = response.data;
          this.offerEditAuthentication = null;
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    destroyOffer: function(offer) {
      if (confirm("Are you sure you want to delete your offer?")) {
        var index = this.post.offers.indexOf(offer);
        axios.delete(`/api/offers/${offer.id}`).then((response) => {
          console.log("Success", response.data);
          this.$parent.flashMessage =
            "Your offer has been successfully deleted";
          this.post.offers.splice(index, 1);
        });
      }
    },
  },
};
</script>
