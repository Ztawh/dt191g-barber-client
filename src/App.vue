<template>
  <div id="super-container">
    <HeaderComponentVue />
    <WelcomeComponentVue />
    <main>
      <!-- <BarbersComponentVue /> -->
      <BookingComponentVue />
      <AboutComponentVue />
      <ContactComponentVue />

    </main>
  </div>
</template>

<script>
// import BarbersComponentVue from "./components/BarbersComponent.vue";
import BookingComponentVue from "./components/BookingComponent.vue";
import ContactComponentVue from "./components/ContactComponent.vue";
import AboutComponentVue from "./components/AboutComponent.vue";
import HeaderComponentVue from "./components/HeaderComponent.vue";
import WelcomeComponentVue from "./components/WelcomeComponent.vue";

export default {
  name: "App",
  components: {
    // BarbersComponentVue,
    BookingComponentVue,
    ContactComponentVue,
    AboutComponentVue,
    HeaderComponentVue,
    WelcomeComponentVue
}
};
</script>

<style>
/* #app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
} */

/* @import "./assets/css/style.scss"; */
</style>
