<template>
  <div id="super-container">
    <HeaderComponentVue />
    <WelcomeComponentVue />
    <main>
      <BookingComponentVue />
    </main>
    <AboutComponentVue />
    <main>
      <ContactComponentVue />
    </main>
    <FooterComponentVue />
  </div>
</template>

<script>
// import BarbersComponentVue from "./components/BarbersComponent.vue";
import BookingComponentVue from "./components/BookingComponent.vue";
import ContactComponentVue from "./components/ContactComponent.vue";
import AboutComponentVue from "./components/AboutComponent.vue";
import HeaderComponentVue from "./components/HeaderComponent.vue";
import WelcomeComponentVue from "./components/WelcomeComponent.vue";
import FooterComponentVue from "./components/FooterComponent.vue";


export default {
  name: "App",
  components: {
    // BarbersComponentVue,
    BookingComponentVue,
    ContactComponentVue,
    AboutComponentVue,
    HeaderComponentVue,
    WelcomeComponentVue,
    FooterComponentVue
}
};
</script>

<style>
</style>
