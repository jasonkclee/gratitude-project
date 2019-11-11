<template>
  <div>
    <nav-bar about />
    <v-container class="mx-auto white--text">
      <h1 class="text-4xl" align="center">
        Coming Soon
      </h1>
    </v-container>
  </div>
</template>

<script>
import NavBar from "@/components/NavBar.vue";

export default {
  name: "home",
  components: {
    NavBar
  }
};
</script>
