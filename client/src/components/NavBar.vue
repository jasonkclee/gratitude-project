<template>
  <v-container class="mb-12">
    <v-app-bar flat app class="white--text" color="primary">
      <v-toolbar-title :style="{ cursor: 'pointer' }">
        <router-link :to="{ name: 'Home' }" tag="v-toolbar-title"
          >gratitude project</router-link
        >
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn
        class="mx-2 primary lighten-2 text--white"
        rounded
        depressed
        v-if="about"
        :to="{ name: 'About' }"
        >about</v-btn
      >
      <v-btn
        class="mx-2 primary lighten-2 text--white"
        rounded
        depressed
        v-if="token"
        @click="logout"
        >Logout</v-btn
      >

      <v-btn
        class="mx-2 primary lighten-2 text--white"
        rounded
        depressed
        v-else
        :to="{ name: 'Login' }"
        >Login</v-btn
      >
    </v-app-bar>
  </v-container>
</template>

<script>
import Vue from "vue";
import VueRouter from "vue-router";
Vue.use(VueRouter);
export default {
  name: "NavBar",
  props: {
    about: {
      type: Boolean,
      required: false,
      default: false
    },
    hideLoginItem: {
      type: Boolean,
      required: false,
      default: false
    }
  },
  data() {
    return {
      token: localStorage.getItem("token")
    };
  },
  methods: {
    logout() {
      localStorage.removeItem("token");
      console.log("Deleted token from local storage");
      this.$router.push("/login");
    },
    home() {
      this.$router.push("/");
    }
  }
};
</script>
