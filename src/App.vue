<template>
  <div class="min-h-screen bg-background">
    <readPage />
  </div>
</template>

<script>
import readPage from "./components/readPage.vue";

export default {
  name: "App",
  components: {
    readPage,
  },
};
</script>
