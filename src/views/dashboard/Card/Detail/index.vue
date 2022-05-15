<template>
  <div>
    <span style="color: ">{{ title }}</span>
    <div style="font-size: 30px;">{{ currte }}</div>
    <div style="height:60px;border-bottom:1px solid #000;">
      <slot name="cardMian"></slot>
    </div>
    <div style="margin:5px">
      <slot name="footer"> </slot>
    </div>
  </div>
</template>

<script>
export default {
  name: "Detail",
  props: ["title", "currte"],
};
</script>

<style>

</style>