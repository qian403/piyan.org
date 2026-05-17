<template>
  <div class="page">
    <header class="header">
      <p class="header-eyebrow">piyan.org</p>
      <h1 class="header-title">
        <span
          class="title-char"
          v-for="(ch, i) in titleChars"
          :key="i"
          :style="{ animationDelay: i * 0.08 + 's' }"
          >{{ ch }}</span
        >
      </h1>
      <p class="header-sub">一個收集網路皮炎的導航站</p>
      <p class="header-desc">
        這裡放了一些人的小窩。每個人都是一個宇宙，<br class="br" />
        只是有些人的宇宙有點味道。
      </p>

      <div class="search-wrap">
        <input
          v-model="searchQuery"
          type="search"
          class="search-input"
          placeholder="搜尋站點名稱、標籤或描述…"
          autocomplete="off"
        />
      </div>
    </header>

    <section class="list">
      <div class="list-inner">
        <SiteCard
          v-for="site in filteredSites"
          :key="site.url"
          :site="site"
          @select="onSelect"
        />
      </div>
      <p v-if="filteredSites.length === 0" class="list-empty">
        沒有找到符合「{{ searchQuery }}」的站點 ¯\_(ツ)_/¯
      </p>
    </section>

    <SiteModal :site="selectedSite" @close="onClose" />

    <footer class="footer">
      <div class="footer-inner">
        <p>入場免費，自備屁眼。</p>
        <p class="footer-repo">
          想加入？
          <a :href="repoUrl" target="_blank" rel="noopener noreferrer">發個 PR</a>
          把你的網站交出來<br />
          編輯 <code>src/data/sites.json</code>
        </p>
      </div>
    </footer>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'
import SiteCard from '@/components/SiteCard.vue'
import SiteModal from '@/components/SiteModal.vue'
import rawSites from '@/data/sites.json'
import { useDevtools } from '@/composables/useDevtools.js'

const { start: startDevtools } = useDevtools()

const sites = ref([])
const searchQuery = ref('')

const filteredSites = computed(() => {
  const q = searchQuery.value.trim().toLowerCase()
  if (!q) return sites.value
  return sites.value.filter(
    (s) =>
      (s.name && s.name.toLowerCase().includes(q)) ||
      (s.tagline && s.tagline.toLowerCase().includes(q)) ||
      (s.description && s.description.toLowerCase().includes(q)),
  )
})

function shuffle(arr) {
  const a = [...arr]
  for (let i = a.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1))
    ;[a[i], a[j]] = [a[j], a[i]]
  }
  return a
}

const titleChars = '屁 眼 派 對'.split('')
const repoUrl = 'https://github.com/qian403/piyan.org'

const selectedSite = ref(null)

function onSelect(site) {
  selectedSite.value = site
}

function onClose() {
  selectedSite.value = null
}

const pageTitle = '屁眼派對 — piyan.org'
const marqueeTitles = ['哈哈屁眼派對', pageTitle]
let titleIndex = 0
let titleTimer = null

onMounted(() => {
  sites.value = shuffle(rawSites)
  titleTimer = setInterval(() => {
    titleIndex = (titleIndex + 1) % marqueeTitles.length
    document.title = marqueeTitles[titleIndex]
  }, 1500)

  startDevtools()
})

onUnmounted(() => {
  if (titleTimer) clearInterval(titleTimer)
})
</script>

<style scoped>
/* ── Layout ── */

.page {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

/* ── Header (centered, max-width) ── */

.header {
  max-width: 40rem;
  width: 100%;
  margin: 0 auto;
  padding: 3rem 1.5rem 1.5rem;
  text-align: center;
}

.header-eyebrow {
  font-size: 0.75rem;
  font-weight: 700;
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: #b8a48a;
  margin-bottom: 0.75rem;
}

.header-title {
  font-size: 3rem;
  font-weight: 900;
  letter-spacing: 0.3em;
  color: #3d2c2c;
  line-height: 1.2;
  margin-bottom: 1rem;
}

.title-char {
  display: inline-block;
  animation: bounceIn 0.5s cubic-bezier(0.34, 1.56, 0.64, 1) both;
}

@keyframes bounceIn {
  0% {
    opacity: 0;
    transform: translateY(-1rem) scale(0.8);
  }
  100% {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

.header-sub {
  font-size: 1rem;
  font-weight: 700;
  color: #e8a87c;
  margin-bottom: 0.5rem;
}

.header-desc {
  font-size: 0.875rem;
  color: #9a8a7a;
  line-height: 1.7;
}

.br {
  display: none;
}

@media (min-width: 480px) {
  .br {
    display: inline;
  }
  .header-title {
    font-size: 3.5rem;
  }
}

/* ── Search ── */

.search-wrap {
  max-width: 40rem;
  width: 100%;
  margin: 1.5rem auto 0;
  padding: 0 1.5rem;
}

.search-input {
  width: 100%;
  padding: 0.75rem 1.25rem;
  border: 2px solid #ede3d3;
  border-radius: 2rem;
  background: #fff;
  font: inherit;
  font-size: 0.9375rem;
  color: #3d2c2c;
  outline: none;
  transition: border-color 0.2s, box-shadow 0.2s;
}

.search-input::placeholder {
  color: #c0b0a0;
}

.search-input:focus {
  border-color: #e8a87c;
  box-shadow: 0 0 0 3px rgba(232, 168, 124, 0.2);
}

/* ── List (full-width grid) ── */

.list-empty {
  text-align: center;
  color: #9a8a7a;
  font-size: 0.9375rem;
  padding: 3rem 0;
}

.list {
  flex: 1;
  padding: 1.5rem;
}

.list-inner {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 0.75rem;
  max-width: 120rem;
  margin: 0 auto;
}

/* ── Footer (centered, max-width) ── */

.footer {
  margin-top: auto;
}

.footer-inner {
  max-width: 40rem;
  width: 100%;
  margin: 0 auto;
  padding: 2rem 1.5rem;
  text-align: center;
  border-top: 1px solid #ede3d3;
}

.footer-inner p {
  font-size: 0.8125rem;
  color: #b8a48a;
}

.footer-repo {
  margin-top: 0.5rem;
}

.footer-repo a {
  color: #e8a87c;
  font-weight: 700;
  text-decoration: none;
}

.footer-repo a:hover {
  text-decoration: underline;
}
</style>
