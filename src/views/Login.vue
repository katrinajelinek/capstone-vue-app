<template>
  <div class="login">
    <div class="replay-box">
      <div class="row">
        <div class="container">
          <div class="col-sm-12">
            <h3>Login</h3>
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
              <div class="col-sm-6">
                <div class="form-group">
                  <input
                    type="emil"
                    name="name"
                    class="form-control"
                    required="required"
                    placeholder="Email"
                    v-model="email"
                  />
                </div>
              </div>
              <div class="col-sm-6">
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
      </div>
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
          this.$parent.flashMessage = "Successfully logged in!";
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
