<template>
  <v-app>
    <v-main>
      <CountryClock />
    </v-main>
  </v-app>
</template>

<script>
// import HelloWorld from "./components/HelloWorld";
import CountryClock from "./components/CountryClock";

export default {
  name: "App",

  components: {
    // HelloWorld,
    CountryClock,
  },

  data: () => ({
    //
  }),
};
</script>
<style>
.my-container {
  max-width: unset !important;
}
</style>
