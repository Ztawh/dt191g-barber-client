<template>
  <div>
    <!-- <HelloWorld msg="Welcome to Your Vue.js App" /> -->
    <TestComponentVue />
    <BarbersComponentVue />
    <BookingComponentVue />
  </div>
</template>

<script>
//import HelloWorld from "./components/HelloWorld.vue";
import TestComponentVue from "./components/TestComponent.vue";
import BarbersComponentVue from "./components/BarbersComponent.vue";
import BookingComponentVue from "./components/BookingComponent.vue";

export default {
  name: "App",
  components: {
    //HelloWorld,
    TestComponentVue,
    BarbersComponentVue,
    BookingComponentVue
},
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
