<template lang="">
  <div class="drag" ref="element" :style="style" @mousedown="handleMouseDown">
    <div class="resizers" :class="{ active: isActive }">
      <div
        class="resizer top-left"
        :class="{ 'd-none': isActive}"
      ></div>
      <div
        class="resizer top-right"
        :class="{ 'd-none': isActive}"
      ></div>
      <div
        class="resizer bottom-left"
        :class="{ 'd-none': isActive}"
      ></div>
      <div
        class="resizer bottom-right"
        :class="{ 'd-none': isActive}"
      ></div>
      
    </div>
  </div>
</template>
<script setup>
import { onMounted, onUnmounted, ref, defineProps } from "vue";
const props = defineProps({
  isActive: {
    type: Boolean,
    default: true,
  },
});

const isMouse = ref(false);
const element = ref();
const isResize = ref(false);
const style = ref({
  transform: "",
});
const shift = ref({
  x: 0,
  y: 0,
});
let xOffset = 0;
let yOffset = 0;
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
  shift.value.x = e.clientX - xOffset;
  shift.value.y = e.clientY - yOffset;

  isMouse.value = true;
};
const handleMouseMove = (e) => {
  console.log(isMouse.value && !isResize.valu);
  if (isMouse.value && !isResize.value) {
    const left = e.clientX - shift.value.x;
    const top = e.clientY - shift.value.y;

    xOffset = left;
    yOffset = top;
    style.value.transform = `translate(${left}px, ${top}px)`;
  }
};
const handleMouseDownResize = (e) => {
  const element = e.currentTarget;
};
const handleMouseSeup = (e) => {
  isMouse.value = false;
};

onMounted(() => {
  const resizers = element.value.querySelectorAll(".resizer");
  const minimum_size = 20;
  let original_width = 0;
  let original_height = 0;
  let original_x = 0;
  let original_y = 0;
  let original_mouse_x = 0;
  let original_mouse_y = 0;

  for (let i = 0; i < resizers.length; i++) {
    const currentResizer = resizers[i];

    currentResizer.onmousedown = (e) => {
      isResize.value = true;

      e.preventDefault();
      original_width = parseFloat(
        getComputedStyle(element.value, null)
          .getPropertyValue("width")
          .replace("px", "")
      );
      original_height = parseFloat(
        getComputedStyle(element.value, null)
          .getPropertyValue("height")
          .replace("px", "")
      );
      original_x = element.value.getBoundingClientRect().left;
      original_y = element.value.getBoundingClientRect().top;
      original_mouse_x = e.pageX;
      original_mouse_y = e.pageY;
      document.addEventListener("mousemove", resize);

      document.addEventListener("mouseup", (e) => {
        isResize.value = false;
        document.removeEventListener("mousemove", resize);
      });
    };

    function resize(e) {
      if (currentResizer.classList.contains("bottom-right")) {
        const width = original_width + (e.pageX - original_mouse_x);
        const height = original_height + (e.pageY - original_mouse_y);
        if (width > minimum_size) {
          style.value.width = width + "px";
        }
        if (height > minimum_size) {
          style.value.height = height + "px";
        }
      } else if (currentResizer.classList.contains("bottom-left")) {
        const height = original_height + (e.pageY - original_mouse_y);
        const width = original_width - (e.pageX - original_mouse_x);
        if (height > minimum_size) {
          style.value.height = height + "px";
        }
        if (width > minimum_size) {
          style.value.width = width + "px";
          style.value.transform =
            original_x + (e.pageX - original_mouse_x) + "px";
        }
      } else if (currentResizer.classList.contains("top-right")) {
        const width = original_width + (e.pageX - original_mouse_x);
        const height = original_height - (e.pageY - original_mouse_y);
        if (width > minimum_size) {
          style.value.width = width + "px";
        }
        if (height > minimum_size) {
          style.value.height = height + "px";
          // style.value.top = original_y + (e.pageY - original_mouse_y) + "px";
        }
      } else {
        const width = original_width - (e.pageX - original_mouse_x);
        const height = original_height - (e.pageY - original_mouse_y);
        if (width > minimum_size) {
          style.value.width = width + "px";
          // style.value.left = original_x + (e.pageX - original_mouse_x) + "px";
        }
        if (height > minimum_size) {
          style.value.height = height + "px";
          // style.value.top = original_y + (e.pageY - original_mouse_y) + "px";
        }
      }
    }
  }
  document.body.addEventListener("mousemove", handleMouseMove);
  document.body.addEventListener("mouseup", handleMouseSeup);
});
onUnmounted(() => {
  document.body.removeEventListener("mousemove", handleMouseMove);
  document.body.removeEventListener("mouseup", handleMouseSeup);
});
</script>
<style lang="scss">
.drag {
  width: 200px;
  height: 200px;
  background: #000;
  border: 1px solid black;
  resize: both;
  position: absolute;
  user-select: none;

  .resizers {
    &.active {
      border: none;
    }
    width: 100%;
    height: 100%;
    border: 3px solid #4286f4;
    box-sizing: border-box;
  }
  .resizers .resizer {
    width: 10px;
    height: 10px;
    border-radius: 50%;
    background: white;
    border: 3px solid #4286f4;
    position: absolute;
  }
  .resizers .resizer.top-left {
    left: -5px;
    top: -5px;
    cursor: nwse-resize; /*resizer cursor*/
  }
  .resizers .resizer.top-right {
    right: -5px;
    top: -5px;
    cursor: nesw-resize;
  }
  .resizers .resizer.bottom-left {
    left: -5px;
    bottom: -5px;
    cursor: nesw-resize;
  }
  .resizers .resizer.bottom-right {
    right: -5px;
    bottom: -5px;
    cursor: nwse-resize;
  }
}
</style>