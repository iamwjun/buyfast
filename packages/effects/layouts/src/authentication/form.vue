<script setup lang="ts">
import { computed } from 'vue';

defineOptions({
  name: 'AuthenticationFormView',
});

const props = defineProps<{
  dataSide?: 'bottom' | 'left' | 'right' | 'top';
}>();

const isRightPanel = computed(() => props.dataSide === 'right');
</script>

<template>
  <div
    :class="
      isRightPanel
        ? 'auth-form-view auth-form-view--right'
        : 'relative flex-col-center bg-background px-6 py-10 lg:flex-initial lg:px-8 dark:bg-background-deep'
    "
  >
    <slot></slot>
    <!-- Router View with Transition and KeepAlive -->
    <RouterView v-slot="{ Component, route }">
      <Transition appear mode="out-in" name="slide-right">
        <KeepAlive :include="['Login']">
          <component
            :is="Component"
            :key="route.fullPath"
            :class="
              isRightPanel
                ? 'side-content side-content--right'
                : 'side-content mt-6 w-full sm:mx-auto md:max-w-md'
            "
            :data-side="dataSide"
          />
        </KeepAlive>
      </Transition>
    </RouterView>

    <!-- Footer Copyright -->

    <div
      :class="
        isRightPanel
          ? 'auth-form-view__copyright auth-form-view__copyright--right'
          : 'absolute bottom-3 flex text-center text-xs text-muted-foreground'
      "
    >
      <slot name="copyright"> </slot>
    </div>
  </div>
</template>

<style scoped>
.auth-form-view--right {
  position: relative;
  display: flex;
  justify-content: center;
  width: 100%;
  padding: 0 1rem 4rem;
  background: transparent;
}

.side-content--right {
  width: 100%;
  max-width: min(95vw, 539px);
}

.auth-form-view__copyright--right {
  position: absolute;
  bottom: 0.75rem;
  left: 50%;
  display: flex;
  font-size: 0.75rem;
  color: hsl(var(--muted-foreground));
  text-align: center;
  transform: translateX(-50%);
}

@media (min-width: 1024px) {
  .auth-form-view--right {
    min-height: auto;
    padding: 0;
  }

  .side-content--right {
    max-width: 539px;
  }

  .auth-form-view__copyright--right {
    bottom: -2rem;
  }
}
</style>
