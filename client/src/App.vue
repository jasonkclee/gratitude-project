<template>
  <v-app class="text-white antialiased">
    <router-view />
  </v-app>
</template>
<style>
body {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}
#app {
  display: flex;
  flex-direction: column;
  flex-grow: 1;
  font-family: "Rubik", sans-serif;
  background: linear-gradient(to bottom right, #34a7ff 55%, #45cafd) no-repeat
    fixed;
}
button {
  outline: none !important;
}
*:focus {
  outline: none !important;
}
</style>

<script>
export default {
  name: "App",

  data: () => ({
    //
  })
};
</script>
