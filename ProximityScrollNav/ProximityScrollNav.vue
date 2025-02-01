<template>
  <div class="relative top-[-50px] justify-center h-[inherit]">
    <div
      ref="navSelectorEl"
      class="absolute-center-y h-[80px] w-full bg-gray-300"
    ></div>
    <div
      ref="navContainerEl"
      class="absolute top-1/2 -translate-y-1/2 flex flex-col justify-center h-full w-full no-scrollbar overflow-y-scroll"
    >
      <ul class="text-center text-white">
        <li
          ref="navItemEl1"
          class="text-3xl font-black py-[4px] shadow-md mt-[230px]"
        >
          NavItem1
        </li>
        <li ref="navItemEl2" class="text-3xl font-black py-[4px] shadow-md">
          NavItem2
        </li>
        <li
          ref="navItemEl3"
          class="text-3xl font-black py-[4px] shadow-md mb-[115px]"
        >
          NavItem3
        </li>
      </ul>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, watch, onMounted } = 'vue'
import { useElementBounding, useScroll } from '@vueuse/core'

const navSelectorEl = ref<HTMLElement | null>(null)
const navContainerEl = ref<HTMLElement | null>(null)
const navItemEl1 = ref<HTMLElement | null>(null)
const navItemEl2 = ref<HTMLElement | null>(null)
const navItemEl3 = ref<HTMLElement | null>(null)

const { top: selectorTop, bottom: selectorBottom } = useElementBounding(navSelectorEl)
const { y: scrollY } = useScroll(navContainerEl, { behavior: 'smooth' })

onMounted(() => {
  watch(
    scrollY,
    () => {
      if (!navContainerEl.value) return
      updateNavItemStyle(navItemEl1.value)
      updateNavItemStyle(navItemEl2.value)
      updateNavItemStyle(navItemEl3.value)
    },
    { immediate: true }
  )
})

function updateNavItemStyle(el: HTMLElement | null) {
  if (!el) return
  const { top: elTop, bottom: elBottom } = el.getBoundingClientRect()
  const threshold = 50
  let diff = 0
  if (elTop < selectorTop.value) {
    diff = selectorTop.value - elTop
  } else if (elBottom > selectorBottom.value) {
    diff = elBottom - selectorBottom.value
  }
  const proximity = (threshold - diff) / threshold + 0.5
  const ratio = proximity > 0 ? proximity : 0
  el.style.transform = `scale(${ratio})`
  el.style.opacity = `${ratio}`
}
</script>

<style scoped>
.no-scrollbar::-webkit-scrollbar {
  display: none;
}

/* Hide scrollbar for IE, Edge, and Firefox */
.no-scrollbar {
  -ms-overflow-style: none; /* IE and Edge */
  scrollbar-width: none; /* Firefox */
}
</style>
