<template>
  <div>
    <nav-bar hideLoginItem />
    <login-form />
  </div>
</template>

<script>
import NavBar from "@/components/NavBar.vue";
import LoginForm from "@/components/LoginForm.vue";
export default {
  name: "login",
  components: {
    NavBar,
    LoginForm
  }
};
</script>

<style>
.loginPage {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>
