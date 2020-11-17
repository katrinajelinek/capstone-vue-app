<template>
  <div id="sub-page" class="posts-show">
    <div id="main-blog">
      <div class="container padding-top">
        <div class="row section-title text-center">
          <div class="col-sm-8 col-sm-offset-2">
            <h1>{{ post.plant_type }}</h1>
            <h3>
              Clipped by:
              <router-link :to="`/users/${post.user_id}`">
                <p>{{ post.user.first_name }} {{ post.user.last_name }}</p>
              </router-link>
            </h3>
            <br />
            <div v-if="post.offer_accepted">
              <h5>An offer has been accepted</h5>
            </div>
          </div>
        </div>
        <div class="row">
          <div id="content" class="full-width site-content col-md-12">
            <div class="post">
              <div class="col-sm-12">
                <div class="entry-thumbnail">
                  <div v-if="post.image_url">
                    <img class="img-responsive" :src="post.image_url" alt="" />
                  </div>
                  <div v-else>
                    <img
                      class="img-responsive"
                      src="/images/default-post-picture.jpg"
                      alt=""
                    />
                  </div>
                </div>
              </div>
              <div class="post-content">
                <h2 class="entry-title">
                  <div v-if="$parent.getUserId() == post.user_id">
                    <router-link :to="`/posts/${post.id}/edit`"
                      >Edit your clipping</router-link
                    >
                  </div>
                </h2>
                <div class="entry-summary">
                  <h3>Trade for: {{ post.trade_for }}</h3>
                  <p>Description: {{ post.description }}</p>
                  <p>Location: {{ post.location }}</p>
                  <h3>Tags:</h3>
                  <div v-for="tag in post.tags">
                    <p>{{ tag.name }}</p>
                  </div>
                  <p>Created {{ relativeDate(post.created_at) }}</p>
                  <div v-if="$parent.getUserId() == post.user_id">
                    <router-link :to="`/posts/${post.id}/edit`"
                      >Edit</router-link
                    >
                  </div>
                </div>
              </div>
            </div>
            <!--/post-->
            <div class="media-wrapper">
              <div class="comments-area">
                <h2 class="comments-heading">Offers</h2>
                <ul class="media-list">
                  <li
                    class="media"
                    v-for="offer in orderBy(post.offers, 'created_at', -1)"
                  >
                    <div class="post-comment">
                      <div v-if="offer.accepted === true">
                        <h3>Accepted</h3>
                      </div>
                      <router-link
                        class="pull-left"
                        :to="`/users/${offer.user_id}`"
                      >
                        <img
                          class="media-object"
                          :src="offer.user.image_url"
                          alt=""
                        />
                      </router-link>
                      <div class="media-body">
                        <h3>
                          <router-link :to="`/users/${offer.user_id}`">
                            {{ offer.user.first_name }}
                            {{ offer.user.last_name }}
                          </router-link>
                          said {{ relativeDate(offer.created_at) }}
                        </h3>
                        <h5>
                          {{ offer.message }}
                        </h5>
                        <img :src="offer.image_url" alt="" />
                      </div>
                    </div>
                    <div v-if="$parent.getUserId() == post.user_id">
                      <div v-if="!post.offer_accepted">
                        <button
                          v-on:click="updateOfferAccept(offer)"
                          class="btn btn-primary pull-right"
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
                        v-on:click="offerEditAuthentication = offer.id"
                        class="btn btn-primary pull-right"
                      >
                        Edit Offer
                      </button>
                    </div>
                    <!--/offersupdate-area-->
                    <div
                      v-if="offerEditAuthentication === offer.id"
                      class="replay-box"
                    >
                      <div class="row">
                        <div class="col-sm-12">
                          <h3>Edit Offer</h3>
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
                            <div class="col-sm-6">
                              <div class="form-group">
                                <textarea
                                  name="message"
                                  id="message"
                                  required="required"
                                  class="form-control"
                                  rows="7"
                                  placeholder="Your Message"
                                  v-model="offer.message"
                                ></textarea>
                              </div>
                            </div>
                            <div class="col-sm-6">
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
                              <button
                                type="submit"
                                class="btn btn-primary pull-right"
                              >
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
                    </div>
                    <!--/Repaly Box-->
                  </li>
                  <br />
                  <br />
                </ul>
              </div>
              <!--/offers-area-->
              <div class="replay-box">
                <div class="row">
                  <div class="col-sm-12">
                    <h3>Leave an Offer</h3>
                    <ul>
                      <li class="text-danger" v-for="error in errors">
                        {{ error }}
                      </li>
                    </ul>
                    <form
                      v-on:submit.prevent="createOffer()"
                      id="comment-form"
                      class="row"
                      name="comment-form"
                      method="post"
                    >
                      <div class="col-sm-6">
                        <div class="form-group">
                          <textarea
                            name="message"
                            id="message"
                            required="required"
                            class="form-control"
                            rows="7"
                            placeholder="Your Message"
                            v-model="message"
                          ></textarea>
                        </div>
                      </div>
                      <div class="col-sm-6">
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
                        <button
                          type="submit"
                          class="btn btn-primary pull-right"
                        >
                          Place Offer
                        </button>
                      </div>
                    </form>
                  </div>
                </div>
              </div>
              <!--/Repaly Box-->
            </div>
            <!--/media-wrapper-->
          </div>
        </div>
      </div>
    </div>
    <!--/#main-blog-->
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
