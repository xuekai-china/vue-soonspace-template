<template>
  <div id="app">
    <SoonSpace ref="bimScene" />
  </div>
</template>

<script>
import SoonSpace from "./components/SoonSpace";

export default {
  name: "app",
  components: {
    SoonSpace,
  },
};
</script>

<style>
* {
  margin: 0;
}
#app {
  position: relative;
  width: 100vw;
  height: 100vh;
}
</style>
