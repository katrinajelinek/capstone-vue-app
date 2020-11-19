<template>
  <div class="login">
    <div id="our-team">
      <!-- our-team -->
      <div class="container text-center our-team padding-top padding-bottom">
        <div class="container text-center our-team padding-bottom">
          <div class="row section-title text-center">
            <div class="col-sm-8 col-sm-offset-2">
              <h1>Login</h1>
            </div>
          </div>
          <ul>
            <li class="text-danger" v-for="error in errors">{{ error }}</li>
          </ul>
          <form
            id="comment-form"
            class="row"
            name="comment-form"
            method="post"
            v-on:submit.prevent="submit()"
          >
            <div class="col-sm-12">
              <div class="form-group">
                <input
                  type="email"
                  name="name"
                  class="form-control"
                  required="required"
                  placeholder="Email"
                  v-model="email"
                />
              </div>
            </div>
            <div class="col-sm-12">
              <div class="form-group">
                <input
                  type="password"
                  name="name"
                  class="form-control"
                  required="required"
                  placeholder="Password"
                  v-model="password"
                />
              </div>
            </div>
            <div class="col-sm-12 form-group">
              <button type="submit" class="btn btn-primary pull-right">
                Login
              </button>
            </div>
          </form>
        </div>
      </div>
      <!-- #/ our-team -->
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      email: "",
      password: "",
      errors: [],
      flashMessage: "",
    };
  },
  methods: {
    submit: function() {
      var params = {
        email: this.email,
        password: this.password,
      };
      axios
        .post("/api/sessions", params)
        .then((response) => {
          axios.defaults.headers.common["Authorization"] =
            "Bearer " + response.data.jwt;
          localStorage.setItem("jwt", response.data.jwt);
          localStorage.setItem("user_id", response.data.user_id);
          this.$parent.flashMessage = "You are successfully logged in!";
          this.$router.push("/");
        })
        .catch((error) => {
          this.errors = ["Invalid email or password."];
          this.email = "";
          this.password = "";
        });
    },
  },
};
</script>
