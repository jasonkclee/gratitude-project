<template>
  <v-container class="mb-12">
    <v-app-bar app>
      <v-toolbar-title>gratitude project</v-toolbar-title>

      <v-toolbar-items>
        <v-btn v-if="about" :to="{ name: 'About' }" class="mx-1">about</v-btn>
        <v-btn v-if="token" @click="logout" class="mx-1">Logout</v-btn>

        <v-btn v-else :to="{ name: 'Login' }" class="mx-1">Login</v-btn>
      </v-toolbar-items>
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
    }
  }
};
</script>
