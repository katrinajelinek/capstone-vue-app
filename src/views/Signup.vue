<template>
  <div class="signup">
    <div class="replay-box">
      <div class="row">
        <div class="col-sm-12">
          <div class="container">
            <h3>Signup</h3>
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
                    type="text"
                    name="name"
                    class="form-control"
                    required="required"
                    placeholder="First name"
                    v-model="firstName"
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
                    placeholder="Last name"
                    v-model="lastName"
                  />
                </div>
              </div>
              <div class="col-sm-6">
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
              <div class="col-sm-6">
                <div class="form-group">
                  <input
                    type="file"
                    name="name"
                    class="form-control"
                    required="required"
                    placeholder="Uploda an image"
                    v-on:change="setFile($event)"
                    ref="fileInput"
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
              <div class="col-sm-6">
                <div class="form-group">
                  <input
                    type="password"
                    name="name"
                    class="form-control"
                    required="required"
                    placeholder="Password Confirmation"
                    v-model="passwordConfirmation"
                  />
                </div>
              </div>
              <div class="col-sm-12 form-group">
                <button type="submit" class="btn btn-primary pull-right">
                  Submit
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
      firstName: "",
      lastName: "",
      email: "",
      password: "",
      passwordConfirmation: "",
      image: "",
      errors: [],
      flashMessage: "",
    };
  },
  methods: {
    setFile: function(event) {
      if (event.target.files.length > 0) {
        this.image = event.target.files[0];
      }
    },
    submit: function() {
      var formData = new FormData();
      formData.append("first_name", this.firstName);
      formData.append("last_name", this.lastName);
      formData.append("email", this.email);
      formData.append("password", this.password);
      formData.append("password_confirmation", this.passwordConfirmation);
      if (this.image) {
        formData.append("image", this.image);
      }
      axios
        .post("/api/users", formData)
        .then((response) => {
          this.$parent.flashMessage =
            "Your account has been successfully created!";
          this.$router.push("/login");
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
  },
};
</script>
