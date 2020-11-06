<template>
  <div class="users-edit">
    <form v-on:submit.prevent="updateUser()">
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
      <div class="form-group">
        <label>To change password, please enter your password:</label> 
        <input type="password" class="form-control" v-model="user.password">
      </div>
      <input type="submit" class="btn btn-primary" value="Update">
    </form>
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
      firstName: "",
      lastName: "",
      email: "",
      password: "",
      image: ""
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
    updateUser: function() {
      var formData = new FormData();
      formData.append("first_name", this.user.firstName);
      formData.append("last_name", this.user.lastName);
      formData.append("email", this.user.email);
      formData.append("password", this.password);
      formData.append("image", this.image);
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