<template>
  <div id="app">
    <panel :size="25" />
  </div>
</template>

<script>
import panel from "./components/panel";
export default {
  name: "app",
  components: {
    panel
  }
};
</script>
<style>
body {
  margin: 0px;
  padding: 0px;
}
* {
  box-sizing: border-box;
  -moz-box-sizing: border-box;
  -webkit-box-sizing: border-box;
}
</style>
