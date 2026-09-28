<template>
  <div class="auth-layout relative flex min-h-screen items-center justify-center overflow-y-auto p-4 sm:p-6">
    <div class="auth-layout-paper pointer-events-none absolute inset-0"></div>

    <!-- Content Container -->
    <div class="relative z-10 w-full max-w-md">
      <!-- Logo/Brand -->
      <div class="mb-8 text-center">
        <!-- Custom Logo or Default Logo -->
        <template v-if="settingsLoaded">
          <div
            class="auth-layout-mark mb-4 inline-flex h-14 w-14 items-center justify-center overflow-hidden rounded-lg"
          >
            <img :src="siteLogo || '/logo.svg'" alt="Logo" class="h-full w-full object-contain" />
          </div>
          <h1 class="mb-2 text-3xl font-semibold tracking-tight text-gray-900 dark:text-white">
            {{ siteName }}
          </h1>
          <p class="text-sm text-gray-500 dark:text-dark-400">
            {{ siteSubtitle }}
          </p>
        </template>
      </div>

      <!-- Card Container -->
      <div class="card p-6 sm:p-8">
        <slot />
      </div>

      <!-- Footer Links -->
      <div class="mt-6 text-center text-sm">
        <slot name="footer" />
      </div>

      <!-- Copyright -->
      <div class="mt-8 text-center text-xs text-gray-400 dark:text-dark-500">
        &copy; {{ currentYear }} {{ siteName }}. All rights reserved.
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted } from 'vue'
import { useAppStore } from '@/stores'
import { sanitizeUrl } from '@/utils/url'

const appStore = useAppStore()

const siteName = computed(() => appStore.siteName || 'Sub2API')
const siteLogo = computed(() => sanitizeUrl(appStore.siteLogo || '', { allowRelative: true, allowDataUrl: true }))
const siteSubtitle = computed(() => appStore.cachedPublicSettings?.site_subtitle || 'Subscription to API Conversion Platform')
const settingsLoaded = computed(() => appStore.publicSettingsLoaded)

const currentYear = computed(() => new Date().getFullYear())

onMounted(() => {
  appStore.fetchPublicSettings()
})
</script>

<style scoped>
.auth-layout {
  background: var(--paper);
}

.auth-layout-paper {
  background-color: var(--paper);
  background-image: radial-gradient(circle at 1px 1px, rgb(102 181 165 / 11%) 1px, transparent 0);
  background-size: 28px 28px;
  opacity: 0.32;
}

.auth-layout-mark {
  border: 1px solid var(--line-strong);
  background: var(--paper-elevated);
  box-shadow: var(--shadow-sm);
}

html.dark .auth-layout-paper {
  background-image: radial-gradient(circle at 1px 1px, rgb(116 198 177 / 13%) 1px, transparent 0);
}
</style>
