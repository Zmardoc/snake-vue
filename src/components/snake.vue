<template>
  <div>
    <block
      :size="size"
      :type="index === 0 ? 'head' : 'body'"
      v-for="(bodyBlock, index) in body"
      :key="bodyBlock.index"
      :position="bodyBlock"
      :letter="message.length > index && message[index]"
    ></block>
  </div>
</template>
<script>
import block from "./block";
export default {
  name: "snake",
  components: {
    block
  },
  props: ["size", "body", "message"]
};
</script>
