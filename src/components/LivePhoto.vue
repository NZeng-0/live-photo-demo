<script setup lang="ts">
interface Props {
  imgSrc: string
  videoSrc: string
}

defineProps<Props>()

const videoRef = useTemplateRef<HTMLVideoElement>('videoRef')
const hoverTimer = ref<number | null>(null)
const isTouch = ref(false)
const longPressTimer = ref<number | null>(null)
const hasInteracted = ref(false)

function initInteraction() {
  hasInteracted.value = true
}

function startVideo() {
  if (!isTouch.value && hasInteracted.value) {
    // Desktop hover
    hoverTimer.value = window.setTimeout(() => {
      playVideo()
    }, 500)
  }
}

function cancelVideo() {
  if (hoverTimer.value) {
    clearTimeout(hoverTimer.value)
    hoverTimer.value = null
  }
}

function handleTouchStart(e: TouchEvent) {
  // eslint-disable-next-line no-console
  console.log(e)
  isTouch.value = true
  if (hasInteracted.value) {
    longPressTimer.value = window.setTimeout(() => {
      playVideo()
    }, 500)
  }
}

function handleTouchEnd() {
  if (longPressTimer.value) {
    clearTimeout(longPressTimer.value)
    longPressTimer.value = null
  }
}

function playVideo() {
  if (videoRef.value) {
    videoRef.value.style.display = 'block'
    videoRef.value.play()
  }
}

function hideVideo() {
  if (videoRef.value) {
    videoRef.value.style.display = 'none'
  }
}
document.addEventListener('click', initInteraction, { once: true });
</script>

<template>
  <div relative mt-1 class="container">
    <Icon @mouseenter="startVideo" @mouseleave="cancelVideo" />
    <img
      :src="imgSrc" alt="cover" hfull wfull
      @touchstart.prevent="handleTouchStart" @touchend.prevent="handleTouchEnd"
    >
    <video ref="videoRef" :src="videoSrc" style="display: none;" absolute left-0 top-0 hfull wfull @ended="hideVideo" />
  </div>
</template>
