<template>
  <span class="whitespace-nowrap">
    {{ currentText }}
    <span v-if="cursor && !hideCursor" class="animate-blink font-normal">
      |</span
    >
  </span>
</template>

<script lang="ts" setup>
import { ref, onMounted, onUnmounted } from "vue";
import { useTimeoutFn } from "@vueuse/core";

interface Props {
  strings: string[];
  typingSpeed?: number;
  backspaceSpeed?: number;
  delayBeforeStart?: number;
  loop?: boolean;
  cursor?: boolean;
  hideCursorAfter?: boolean;
  backspace?: boolean;
}

const {
  strings,
  typingSpeed = 100,
  backspaceSpeed = 50,
  delayBeforeStart = 1000,
  loop = true,
  cursor = true,
  hideCursorAfter = false,
  backspace = true,
} = defineProps<Props>();

const emits = defineEmits<{
  (e: "update", value: string): void;
}>();

const currentText = ref("");
const currentIndex = ref(0);
const currentStringIndex = ref(0);
const hideCursor = ref(false);

// Timeout for the initial delay
const { start: startDelay, stop: stopDelay } = useTimeoutFn(
  () => {
    typeText();
  },
  delayBeforeStart,
  { immediate: false }
);

// Timeout for typing
const { start: startTyping, stop: stopTyping } = useTimeoutFn(
  () => {
    typeText();
  },
  typingSpeed,
  { immediate: false }
);

// Timeout for backspacing
const { start: startBackspace, stop: stopBackspace } = useTimeoutFn(
  () => {
    backspaceText();
  },
  backspaceSpeed,
  { immediate: false }
);

const { start: startPause, stop: stopPause } = useTimeoutFn(
  () => {
    backspaceText();
  },
  2000,
  { immediate: false }
);

function typeText() {
  if (currentIndex.value < strings[currentStringIndex.value].length) {
    currentText.value += strings[currentStringIndex.value].charAt(
      currentIndex.value
    );
    currentIndex.value++;
    startTyping();
  } else {
    if (hideCursorAfter) hideCursor.value = true;
    emits("update", currentText.value);
    startPause();
  }
}

function backspaceText() {
  if (!backspace) return;
  if (currentIndex.value > 0) {
    currentText.value = currentText.value.slice(0, -1);
    currentIndex.value--;
    startBackspace();
  } else {
    if (loop) {
      currentStringIndex.value =
        (currentStringIndex.value + 1) % strings.length;
      startDelay();
    }
  }
}

onMounted(() => {
  startDelay();
});

onUnmounted(() => {
  stopDelay();
  stopTyping();
  stopBackspace();
  stopPause();
});
</script>

<style scoped>
.animate-blink {
  animation: "blink 0.8s linear infinite";
}

@keyframes blink {
  0% {
    opacity: 1;
  }
  50% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
</style>
