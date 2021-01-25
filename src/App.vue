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
.test-tool-box {
  position: fixed;
  bottom: 0;
  width: 100%;
  height: auto;
  margin: 0 auto;
  text-align: center;
  pointer-events: none;
}
button {
  margin: 4px;
  pointer-events: all;
}
</style>
