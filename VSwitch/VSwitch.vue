<template>
  <div class="flex items-center justify-center w-[30px] cursor-pointer">
    <div class="relative w-full h-[10px] rounded-full bg-slate-300">
      <div
        class="absolute left-0 top-1/2 -translate-y-1/2 w-[15px] h-[15px] rounded-full transition-all duration-300 ease-in-out"
        :class="
          enabled
            ? 'translate-x-0 bg-blue-500'
            : 'translate-x-[15px] bg-slate-300'
        "
      ></div>
    </div>
  </div>
</template>

<script lang="ts" setup>
type Props = { enabled: boolean };
defineProps<Props>();
</script>
