<template>
  <div id="app">
    <header id="navigation">
      <div class="navbar navbar-static-top" role="banner">
        <div class="container">
          <div class="row header-section">
            <div class="col-sm-3">
              <div class="navbar-header">
                <button
                  type="button"
                  class="navbar-toggle"
                  data-toggle="collapse"
                  data-target=".navbar-collapse"
                >
                  <span class="sr-only">Toggle navigation</span>
                  <span class="icon-bar"></span>
                  <span class="icon-bar"></span>
                  <span class="icon-bar"></span>
                </button>
                <a class="navbar-brand" href="index.html">
                  <h1>
                    <img
                      class="img-responsive"
                      src="/images/logo.png"
                      alt="logo"
                    />
                  </h1>
                </a>
              </div>
            </div>
            <div class="col-sm-9">
              <nav class="collapse navbar-collapse navbar-right">
                <ul class="nav navbar-nav">
                  <li><router-link to="/">Clippings</router-link></li>
                  <li>
                    <router-link to="/posts/new">New Clipping</router-link>
                  </li>
                  <li v-if="!isLoggedIn()">
                    <router-link to="/login">Login</router-link>
                  </li>
                  <li v-if="isLoggedIn()">
                    <router-link to="/logout">Logout</router-link>
                  </li>
                  <li v-if="!isLoggedIn()">
                    <router-link to="/signup">Signup</router-link>
                  </li>
                  <li v-if="isLoggedIn()">
                    <router-link :to="`/users/${getUserId()}`"
                      >Profile</router-link
                    >
                  </li>
                </ul>
              </nav>
              <div class="topbar-icons">
                <span><i class="fa fa-search"></i></span>
              </div>
              <div class="search">
                <form role="form">
                  <input
                    type="text"
                    class="search-form"
                    autocomplete="off"
                    placeholder="Write something and press enter"
                  />
                  <span class="search-close"><i class="fa fa-times"></i></span>
                </form>
              </div>
            </div>
          </div>
        </div>
      </div>
    </header>
    <!--/#navigation-->
    <!--/#navigation-->
    <!-- <header id="navigation">
      <div class="navbar navbar main-nav" role="banner">
        <div class="container">
          <div class="row header-section">
            <div class="col-sm-3">
              <div class="navbar-header">
                <button
                  type="button"
                  class="navbar-toggle"
                  data-toggle="collapse"
                  data-target=".navbar-collapse"
                >
                  <span class="sr-only">Toggle navigation</span>
                  <span class="icon-bar"></span>
                  <span class="icon-bar"></span>
                  <span class="icon-bar"></span>
                </button>
                <a class="navbar-brand" href="index.html">
                  <h1>
                    <img
                      class="img-responsive"
                      src="images/logo.png"
                      alt="logo"
                    />
                  </h1>
                </a>
              </div>
            </div>
            <div class="col-sm-9">
              <nav class="collapse navbar-collapse navbar-right">
                <ul class="nav navbar-nav">
                  <li><router-link to="/posts">Clippings</router-link></li>
                  <li>
                    <router-link to="/posts/new">New Clipping</router-link>
                  </li>
                  <li v-if="!isLoggedIn()">
                    <router-link to="/login">Login</router-link>
                  </li>
                  <li v-if="isLoggedIn()">
                    <router-link to="/logout">Logout</router-link>
                  </li>
                  <li v-if="!isLoggedIn()">
                    <router-link to="/signup">Signup</router-link>
                  </li>
                  <li v-if="isLoggedIn()">
                    <router-link :to="`/users/${getUserId()}`"
                      >Profile</router-link
                    >
                  </li>
                </ul>
              </nav>
              <div class="topbar-icons">
                <span><i class="fa fa-search"></i></span>
              </div>
              <div class="search">
                <form role="form">
                  <input
                    type="text"
                    class="search-form"
                    autocomplete="off"
                    placeholder="Write something and press enter"
                  />
                  <span class="search-close"><i class="fa fa-times"></i></span>
                </form>
              </div>
            </div>
          </div>
        </div>
      </div>
    </header> -->

    <router-view />

    <!-- footer -->
    <footer id="footer">
      <!-- footer-bottom -->
      <div class="footer-bottom">
        <div class="container">
          <div class="row">
            <div class="col-sm-6">
              <!-- <ul class="footer-menu list-inline">
                <li><router-link to="/posts">Clippings</router-link></li>
                <li>
                  <router-link to="/posts/new">New Clipping</router-link>
                </li>
                <li v-if="!isLoggedIn()">
                  <router-link to="/login">Login</router-link>
                </li>
                <li v-if="isLoggedIn()">
                  <router-link to="/logout">Logout</router-link>
                </li>
                <li v-if="!isLoggedIn()">
                  <router-link to="/signup">Signup</router-link>
                </li>
                <li v-if="isLoggedIn()">
                  <router-link :to="`/users/${getUserId()}`"
                    >Profile</router-link
                  >
                </li>
              </ul> -->
            </div>

            <div class="col-sm-6">
              <div class="copy-right text-right">
                <p>
                  &copy; Copyright <strong>Protocol</strong> by
                  <a href="themeregion.com">Theme Region</a>
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <!-- footer-bottom -->
    </footer>
    <!--/#footer-->
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function() {
    return {};
  },
  methods: {
    isLoggedIn: function() {
      return localStorage.getItem("jwt");
    },
    getUserId: function() {
      return localStorage.getItem("user_id");
    },
  },
};
</script>
