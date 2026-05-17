<template>
  <button class="card" @click="$emit('select', site)">
    <span class="card-avatar">
      <img
        v-if="!imgError"
        :src="faviconUrl"
        :alt="site.name"
        class="card-favicon"
        @error="imgError = true"
      />
      <span v-else class="card-emoji">{{ site.emoji }}</span>
    </span>
    <div class="card-body">
      <h3 class="card-name">{{ site.name }}</h3>
      <p class="card-desc">{{ shortDesc }}</p>
    </div>
  </button>
</template>

<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  site: { type: Object, required: true },
})

defineEmits(['select'])

const imgError = ref(false)

const MAX_LEN = 22

const shortDesc = computed(() => {
  const d = props.site.description || ''
  if (d.length <= MAX_LEN) return d
  return d.slice(0, MAX_LEN) + '…'
})

const faviconUrl = computed(() => {
  if (props.site.avatar) return props.site.avatar
  try {
    const url = new URL(props.site.url)
    return `https://www.google.com/s2/favicons?domain=${url.hostname}&sz=64`
  } catch {
    return ''
  }
})
</script>

<style scoped>
.card {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 1rem 1.25rem;
  background: #fff;
  border: 2px solid #f0e6d3;
  border-radius: 0.875rem;
  cursor: pointer;
  color: inherit;
  font: inherit;
  text-align: left;
  width: 100%;
  transition: all 0.2s ease;
}

.card:hover {
  animation: rainbow 0.8s linear infinite;
  transform: translateY(-2px);
}

@keyframes rainbow {
  0% {
    border-color: #ff6b6b;
    box-shadow: 0 0 16px rgba(255, 107, 107, 0.25);
  }
  14% {
    border-color: #ffa94d;
    box-shadow: 0 0 16px rgba(255, 169, 77, 0.25);
  }
  28% {
    border-color: #ffd43b;
    box-shadow: 0 0 16px rgba(255, 212, 59, 0.25);
  }
  42% {
    border-color: #69db7c;
    box-shadow: 0 0 16px rgba(105, 219, 124, 0.25);
  }
  57% {
    border-color: #4dabf7;
    box-shadow: 0 0 16px rgba(77, 171, 247, 0.25);
  }
  71% {
    border-color: #9775fa;
    box-shadow: 0 0 16px rgba(151, 117, 250, 0.25);
  }
  85% {
    border-color: #f783ac;
    box-shadow: 0 0 16px rgba(247, 131, 172, 0.25);
  }
  100% {
    border-color: #ff6b6b;
    box-shadow: 0 0 16px rgba(255, 107, 107, 0.25);
  }
}

.card-avatar {
  flex-shrink: 0;
  width: 2.75rem;
  height: 2.75rem;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #fdf6ee;
  border-radius: 0.75rem;
  overflow: hidden;
}

.card-favicon {
  width: 100%;
  height: 100%;
  object-fit: contain;
  display: block;
}

.card-emoji {
  font-size: 1.375rem;
}

.card-body {
  flex: 1;
  min-width: 0;
}

.card-name {
  font-size: 0.9375rem;
  font-weight: 700;
  margin: 0 0 0.125rem;
  color: #3d2c2c;
}

.card-desc {
  font-size: 0.8125rem;
  margin: 0;
  color: #7a6a5a;
  line-height: 1.4;
}
</style>
