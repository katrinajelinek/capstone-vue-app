<template>
  <div class="users-edit">
    <form v-on:submit.prevent="updateUser(user)">
      <h1>Edit Clipping</h1>
      <ul>
        <li class="text-danger" v-for="error in errors">{{ error }}</li>
      </ul>
      <div class="form-group">
        <label>First Name:</label> 
        <input type="text" class="form-control" v-model="user.first_name">
      </div>
      <div class="form-group">
        <label>Last Name:</label> 
        <input type="text" class="form-control" v-model="user.last_name">
      </div>
      <div class="form-group">
        <label>Email:</label> 
        <input type="text" class="form-control" v-model="user.email">
      </div>
      <div class="form-group">
        <label>Image:</label> 
        <input type="file" class="form-control" v-on:change="setFile($event)" ref="fileInput">
      </div>
      <!-- <div class="form-group">
        <label>To change password, please enter your password:</label> 
        <input type="password" class="form-control" v-model="user.password">
      </div> -->
      <input type="submit" class="btn btn-primary" value="Update">
    </form>
    <button v-on:click="passwordUpdateToggle = !passwordUpdateToggle">
        Update Password
      </button>
      <div v-if="passwordUpdateToggle === true">
        <div class="form-group">
          <label>Old Password</label>
          <input type="password" class="form-control" v-model="old_password">
        </div>
        <div class="form-group">
          <label>New Password</label>
          <input type="password" class="form-control" v-model="password">
        </div>
        <div class="form-group">
          <label>Password Confirmation</label>
          <input type="password" class="form-control" v-model="password_confirmation">
        </div>
      </div>
    <button v-on:click="destroyUser()">
        Delete
      </button>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      user: {},
      errors: [],
      old_password: "",
      password: "",
      password_confirmation: "",
      passwordUpdateToggle: false
    };
  },
  created: function() {
    axios.get(`/api/users/${this.$route.params.id}`).then(response => {
      console.log(response.data);
      this.user = response.data;
    });
  },
  methods: {
    setFile: function(event) {
      if (event.target.files.length > 0) {
        this.image = event.target.files[0];
      }
    },
    updateUser: function(user) {
      var formData = new FormData();
      formData.append("first_name", user.first_name);
      formData.append("last_name", user.last_name);
      formData.append("email", user.email);
      formData.append("password", user.password);
      formData.append("old_password", this.old_password);
      formData.append("password", this.password);
      formData.append("password_confirmation", this.password_confirmation);
      if (this.image) {
        formData.append("image", this.image);
      }
      axios
        .patch(`/api/users/${this.user.id}`, formData)
        .then((response) => {
          this.$router.push(`/users/${this.user.id}`);
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
    destroyUser: function () {
      axios.delete(`/api/users/${this.user.id}`).then(response => {
        console.log("Success", response.data);
        this.$router.push("/posts");
      });
    }
  }
};
</script>