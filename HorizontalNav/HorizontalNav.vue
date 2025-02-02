<template>
  <div
    class="relative flex items-center justify-center h-[100px] w-full bg-gray-300"
  >
    <button
      @click="
        handleClickArrow(
          $event.target.classList[0].split('-')[1] as 'left' | 'right'
        )
      "
      class="absolute z-10 top-1/2 -translate-y-1/2 left-1/4 translate-x-[120%] text-white"
      :disabled="currentItem === 0"
    >
      <!-- Custom icon component -->
      <VIcon
        name="icon-left"
        size="7xl"
        :class="{ 'hover:text-shadow': currentItem !== 0 }"
      />
    </button>
    <button
      @click="
        handleClickArrow(
          $event.target.classList[0].split('-')[1] as 'left' | 'right'
        )
      "
      class="absolute z-10 top-1/2 -translate-y-1/2 right-1/4 translate-x-[-120%] text-white"
      :disabled="currentItem === navItems.length - 1"
    >
      <!-- Custom icon component -->
      <VIcon
        name="icon-right"
        size="7xl"
        :class="{ 'hover:text-shadow': currentItem !== navItems.length - 1 }"
      />
    </button>
    <div class="relative h-[80%] w-1/3 text-center overflow-hidden">
      <ul class="absolute left-1/2 -translate-x-1/2 flex gap-[120px]">
        <li
          v-for="(item, i) in navItems"
          :key="item"
          class="z-10 text-white w-[300px] text-7xl font-black cursor-pointer"
          :class="[getVisibility(i), itemAnimation]"
        >
          {{ item }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref } from "vue";
import { useTimeoutFn } from "@vueuse/core";

const navItems = ref(["Item1", "Item2", "Item3"]);
const currentItem = ref(1);
const replacedItem = ref<number | undefined>(undefined);
const itemAnimation = ref("");

const { start } = useTimeoutFn(() => {
  itemAnimation.value = "";
  replacedItem.value = undefined;
}, 500);

const getVisibility = (i: number) => {
  return currentItem.value === i || replacedItem.value === i
    ? "block"
    : "hidden";
};

const handleClickArrow = (dir: "left" | "right") => {
  replacedItem.value = currentItem.value;

  if (dir === "left") {
    currentItem.value--;
    itemAnimation.value = "animate-fly-right";
    start();
  } else {
    currentItem.value++;
    itemAnimation.value = "animate-fly-left";
    start();
  }
};
</script>

<style scoped>
@keyframes fly-left {
  0% {
    transform: "translateX(0)";
  }
  100% {
    transform: "translateX(-70%)";
  }
}

@keyframes fly-right {
  0% {
    transform: "translateX(0)";
  }
  100% {
    transform: "translateX(-70%)";
  }
}

.animate-fly-left {
  animation: fly-left 0.5s ease-in-out;
}

.animate-fly-right {
  animation: fly-right 0.5s ease-in-out;
}

.text-shadow {
  text-shadow: 0px 0px 5px rgba(0, 0, 0, 0.5);
}
</style>
