<template>
  <div id="app">
    <Bible />
  </div>
</template>

<script>
import "tailwindcss/tailwind.css";
import Bible from "@/components/Bible.vue";

export default {
  name: "App",
  components: { Bible },
};
</script>

<style>
html,
body,
#app {
  height: 100%;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
