<template>
  <div id="app">
    <div class="p-12 flex flex-col items-center justify-center">
      <Bible />
    </div>
  </div>
</template>

<script>
import "tailwindcss/tailwind.css";
import Bible from "./components/Bible";
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
