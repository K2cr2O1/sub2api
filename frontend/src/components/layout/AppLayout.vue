<template>
  <div class="app-layout min-h-screen">
    <!-- A quiet paper-toned backdrop keeps dense admin screens readable. -->
    <div class="app-layout-backdrop pointer-events-none fixed inset-0"></div>

    <!-- Sidebar -->
    <AppSidebar />

    <!-- Main Content Area -->
    <div
      class="app-layout-main relative min-h-screen transition-all duration-300"
      :class="[sidebarCollapsed ? 'lg:ml-[72px]' : 'lg:ml-64']"
    >
      <!-- Header -->
      <AppHeader />

      <!-- Main Content -->
      <main class="app-layout-content">
        <slot />
      </main>
    </div>
  </div>
</template>

<script setup lang="ts">
import '@/styles/onboarding.css'
import { computed, onMounted } from 'vue'
import { useAppStore } from '@/stores'
import { useAuthStore } from '@/stores/auth'
import { useOnboardingTour } from '@/composables/useOnboardingTour'
import { useOnboardingStore } from '@/stores/onboarding'
import AppSidebar from './AppSidebar.vue'
import AppHeader from './AppHeader.vue'

const appStore = useAppStore()
const authStore = useAuthStore()
const sidebarCollapsed = computed(() => appStore.sidebarCollapsed)
const isAdmin = computed(() => authStore.user?.role === 'admin')

const { replayTour } = useOnboardingTour({
  storageKey: isAdmin.value ? 'admin_guide' : 'user_guide',
  autoStart: true
})

const onboardingStore = useOnboardingStore()

onMounted(() => {
  onboardingStore.setReplayCallback(replayTour)
})

defineExpose({ replayTour })
</script>

<style scoped>
.app-layout {
  --layout-paper: var(--paper);
  --layout-ink: var(--ink);
  --layout-muted: var(--muted);
  --layout-line: var(--line);
  position: relative;
  overflow-x: clip;
  background: var(--layout-paper);
  color: var(--layout-ink);
}

.app-layout-backdrop {
  z-index: 0;
  background-color: var(--layout-paper);
  background-image:
    linear-gradient(rgb(34 48 46 / 3%) 1px, transparent 1px),
    linear-gradient(90deg, rgb(34 48 46 / 3%) 1px, transparent 1px);
  background-size: 32px 32px;
}

.app-layout-main,
.app-layout-content {
  position: relative;
  z-index: 1;
}

.app-layout-content {
  min-height: calc(100vh - 4rem);
  padding: 1.25rem;
}

@media (min-width: 768px) {
  .app-layout-content {
    padding: 1.5rem;
  }
}

@media (min-width: 1024px) {
  .app-layout-content {
    padding: 2rem;
  }
}

:global(.dark) .app-layout {
  background: var(--layout-paper);
}

:global(.dark) .app-layout-backdrop {
  background-color: var(--layout-paper);
  background-image:
    linear-gradient(rgb(238 241 233 / 3%) 1px, transparent 1px),
    linear-gradient(90deg, rgb(238 241 233 / 3%) 1px, transparent 1px);
}

@media (max-width: 639px) {
  .app-layout-content {
    padding: 1rem;
  }
}
</style>
