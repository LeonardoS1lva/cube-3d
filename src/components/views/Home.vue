<template>
  <div>
    <header>
      <h1 class="title">Trabalhando com 3D em Vue.js</h1>
    </header>
    <main>
      <div class="container">
        <Model />
      </div>
    </main>
  </div>
</template>

<script>
import Model from "@/components/Model.vue";

export default {
  components: {
    Model,
  },
};
</script>

<style scoped>
* {
  font-family: "Arial", sans-serif;
}

.title {
  font-size: 2em;
  text-align: center;
}

header {
  background-color: #333;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 10vh;
}
main {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 90vh;
}

.container {
  width: 90%;
  height: 90%;
  display: flex;
  justify-content: center;
  align-items: center;
  border: 2px solid #333;
  border-radius: 10px;
  overflow: hidden;
}
</style>
