<template>
  <div id="button">
    <button
      class="text-white text-sm p-2 uppercase"
    >
       <slot></slot>
    </button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
export default defineComponent ({
  name: "ButtonComponent"
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
