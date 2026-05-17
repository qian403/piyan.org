<template>
  <Teleport to="body">
    <div v-if="site" class="backdrop" @click.self="$emit('close')">
      <div class="modal" role="dialog" aria-modal="true">
        <button class="close" @click="$emit('close')" aria-label="關閉">✕</button>

        <div class="modal-avatar">
          <img
            v-if="!imgError"
            :src="faviconUrl"
            :alt="site.name"
            class="modal-favicon"
            @error="imgError = true"
          />
          <span v-else class="modal-emoji">{{ site.emoji }}</span>
        </div>

        <h2 class="modal-name">{{ site.name }}</h2>
        <p v-if="site.tagline" class="modal-tagline">{{ site.tagline }}</p>
        <p class="modal-desc">{{ site.description }}</p>

        <a :href="site.url" target="_blank" rel="noopener noreferrer" class="modal-link">
          前往網站 →
        </a>
      </div>
    </div>
  </Teleport>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

const props = defineProps({
  site: { type: Object, default: null },
})

const emit = defineEmits(['close'])

const imgError = ref(false)

const faviconUrl = computed(() => {
  if (!props.site) return ''
  if (props.site.avatar) return props.site.avatar
  try {
    const url = new URL(props.site.url)
    return `https://www.google.com/s2/favicons?domain=${url.hostname}&sz=128`
  } catch {
    return ''
  }
})

function onKeydown(e) {
  if (e.key === 'Escape') emit('close')
}

onMounted(() => window.addEventListener('keydown', onKeydown))
onUnmounted(() => window.removeEventListener('keydown', onKeydown))
</script>

<style scoped>
.backdrop {
  position: fixed;
  inset: 0;
  background: rgba(30, 20, 15, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 100;
  padding: 1.5rem;
  backdrop-filter: blur(2px);
}

.modal {
  background: #fff;
  border-radius: 1.25rem;
  padding: 2.5rem 2rem 2rem;
  max-width: 28rem;
  width: 100%;
  position: relative;
  text-align: center;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
  animation: popIn 0.2s ease;
}

@keyframes popIn {
  0% {
    transform: scale(0.9);
    opacity: 0;
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}

.close {
  position: absolute;
  top: 0.75rem;
  right: 0.75rem;
  width: 2rem;
  height: 2rem;
  border: none;
  background: #f5ede3;
  border-radius: 50%;
  cursor: pointer;
  font-size: 0.875rem;
  color: #7a6a5a;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background 0.15s;
}

.close:hover {
  background: #e8dccc;
}

.modal-avatar {
  width: 4.5rem;
  height: 4.5rem;
  margin: 0 auto 1rem;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #fdf6ee;
  border-radius: 1rem;
  overflow: hidden;
}

.modal-favicon {
  width: 100%;
  height: 100%;
  object-fit: contain;
  display: block;
}

.modal-emoji {
  font-size: 2.25rem;
}

.modal-name {
  font-size: 1.25rem;
  font-weight: 900;
  color: #3d2c2c;
  margin: 0 0 0.25rem;
}

.modal-tagline {
  font-size: 0.875rem;
  color: #e8a87c;
  font-weight: 700;
  margin: 0 0 1rem;
}

.modal-desc {
  font-size: 0.9375rem;
  color: #5a4a3a;
  line-height: 1.7;
  margin: 0 0 1.5rem;
  text-align: left;
}

.modal-link {
  display: inline-block;
  padding: 0.75rem 2rem;
  background: #3d2c2c;
  color: #fff;
  border-radius: 0.75rem;
  font-weight: 700;
  font-size: 0.9375rem;
  text-decoration: none;
  transition: background 0.2s;
}

.modal-link:hover {
  background: #5a3a3a;
}
</style>
