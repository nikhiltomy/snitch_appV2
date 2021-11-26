<template lang="">
  <div>
    <button
      v-for="(attribs, a) in option"
      :key="`item-${a}`"
      @click.prevent="$emit('size-select',{ [o]: attribs.value })"
    >
      {{ attribs.value }}
     
    </button>
    <!-- { [o]: attribs.value } -->
  </div>
</template>
<script>
export default {
    props:['option','o']
};
</script>
<style></style>
