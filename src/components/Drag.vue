<template lang="">
    <div class="drag" ref="element" :style="style" @mousedown="handleMouseDown">
        
    </div>
</template>
<script setup>
import { onMounted, ref } from "vue";
const isMouse = ref(false);
const element = ref();
const coords = ref();
const style = ref({
  transform: "",
});
const shift = ref({
  x: 0,
  y: 0,
});
const getCords = () => {
  const box = element.value.getBoundingClientRect();
  console.log(box);
  return {
    left: box.left,
    top: box.top,
  };
};
const handleMouseDown = (e) => {
  const downCords = getCords();
  shift.value.x = e.clientX - downCords.left;
  shift.value.y = e.clientY - downCords.top;

  console.log(e);

  isMouse.value = true;
};
const handleMouseMove = (e) => {
  if (isMouse.value) {
    const left = e.offsetX;
    const top = e.offsetY;
    style.value.transform = `translate(${left}px, ${top}px)`;
  }
};
const handleMouseSeup = (e) => {
  isMouse.value = false;
};
onMounted(() => {
  document.body.addEventListener("mousemove", handleMouseMove);
  document.body.addEventListener("mouseup", handleMouseSeup);
});
</script>
<style>
.drag {
  width: 200px;
  height: 200px;
  background: #000;
  border: 1px solid black;
}
</style>