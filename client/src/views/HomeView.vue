<template>
  <div class="page">
    <HelloWorld />
  </div>
</template>

<script>
import HelloWorld from "@/components/HelloWorld.vue";
export default {
  components: {
    HelloWorld,
  },
};
</script>

<style scoped>
.page {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
