<template>
  <div id="app">
    <div class="p-12 text-center">
      <div
        class="flex flex-col md:flex-row items-center justify-center space-x-2"
      >
        <span class="text-lg">Bíblias:</span>
        <select
          v-model="version"
          class="p-1 border-2 border-gray-200 focus:outline-none"
        >
          <option value="" selected disabled>Selecione uma versão</option>
          <option value="acf">Almeida Corrigida Fiel</option>
          <option value="nvi">Nova Versão Internacional</option>
        </select>
      </div>
      <Bible :version="version" class="mt-6" />
    </div>
  </div>
</template>

<script>
import "tailwindcss/tailwind.css";
import Bible from "./components/Bible";

export default {
  name: "App",
  components: { Bible },
  data() {
    return {
      version: "",
    };
  },
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
