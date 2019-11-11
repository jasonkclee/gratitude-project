<template>
  <v-container>
    <v-card width="400" class="mx-auto mt-5">
      <v-card-title>
        <h1 class="display-1">Login</h1>
      </v-card-title>
      <v-card-text>
        <v-form>
          <v-text-field
            v-model="input.username"
            label="Username"
            prepend-icon="mdi-account-circle"
            @keyup.enter="login"
          />
          <v-text-field
            :type="showPassword ? 'text' : 'password'"
            v-model="input.password"
            label="Password"
            @keyup.enter="login"
            prepend-icon="mdi-lock"
            :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
            @click:append="showPassword = !showPassword"
          />
        </v-form>
      </v-card-text>
      <v-divider></v-divider>
      <v-card-actions>
        <v-btn class="mx-auto my-2" color="info" @click="login()">Login</v-btn>
      </v-card-actions>
    </v-card>
    <v-alert width="400" class="mt-5 mx-auto" type="error" v-if="errorMsg">
      {{ errorMsg }}</v-alert
    >
  </v-container>
</template>

<script>
import Vue from "vue";
import VueRouter from "vue-router";
import store from "./../store";
Vue.use(VueRouter);
const axios = require("axios").default;
const lsCheck = require("./../LScheck");
const login_end = "http://localhost:5000/api/users/login";
const jwt = require("jsonwebtoken");
// const router = new VueRouter();

export default {
  name: "LoginForm",
  data() {
    return {
      showPassword: false,
      input: {
        username: "",
        password: ""
      },
      errorMsg: null
    };
  },
  mounted() {
    if (lsCheck.hasStorage && localStorage.getItem("token")) {
      var token = localStorage.getItem("token");
      try {
        const exp_check = jwt.decode(token.substring(7, token.length));
        if (Date.now() >= exp_check.exp * 1000) {
          localStorage.removeItem("token");
          throw "token expired";
        }
        //redirects to home page if token is valid
        console.log("user already signed in");
        this.$router.push("/create");
      } catch (err) {
        console.log(err);
      }
    }
  },
  methods: {
    login() {
      var self = this;
      //post username and password to server
      axios
        .post(login_end, {
          email: this.input.username,
          password: this.input.password
        })
        .then(res => {
          this.errorMsg = null;
          axios.defaults.headers.common["Authorization"] = res.data.token;
          if (lsCheck.hasStorage) {
            var decoded = jwt.decode(
              res.data.token.substring(7, res.data.token.length)
            );
            localStorage.setItem("token", res.data.token);
            this.$store.commit("setNameId", decoded.name, decoded.id);
            console.log("the name in storage is " + this.$store.state.name);
            self.$router.push("/create");
          }
        })
        .catch(err => {
          console.log(err);
          this.errorMsg = "Error: ";
          if (err.response.data.email) {
            this.errorMsg = this.errorMsg + err.response.data.email + "\n";
          }
          if (err.response.data.password) {
            this.errorMsg += err.response.data.password;
          }
        });
    }
  }
};
</script>
