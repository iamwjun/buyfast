<script setup lang="ts">
import type { ToolbarType } from './types';

import { computed } from 'vue';

import { preferences } from '@vben/preferences';

import {
  AuthenticationColorToggle,
  AuthenticationLayoutToggle,
  LanguageToggle,
  ThemeToggle,
} from '../widgets';

interface Props {
  placement?: 'bottom-right' | 'top-right';
  toolbarList?: ToolbarType[];
}

defineOptions({
  name: 'AuthenticationToolbar',
});

const props = withDefaults(defineProps<Props>(), {
  placement: 'top-right',
  toolbarList: () => ['color', 'language', 'layout', 'theme'],
});

const showColor = computed(() => props.toolbarList.includes('color'));
const showLayout = computed(() => props.toolbarList.includes('layout'));
const showLanguage = computed(() => props.toolbarList.includes('language'));
const showTheme = computed(() => props.toolbarList.includes('theme'));
</script>

<template>
  <div
    :class="{
      'bottom-4 right-4 md:bottom-8 md:right-8': placement === 'bottom-right',
      'rounded-3xl bg-accent px-3 py-1': toolbarList.length > 1,
      'top-4 right-2': placement === 'top-right',
    }"
    class="absolute z-10 flex-center"
  >
    <!-- Only show on medium and larger screens -->
    <div class="hidden md:flex">
      <AuthenticationColorToggle v-if="showColor" />
      <AuthenticationLayoutToggle v-if="showLayout" />
    </div>
    <!-- Always show Language and Theme toggles -->
    <LanguageToggle v-if="showLanguage && preferences.widget.languageToggle" />
    <ThemeToggle v-if="showTheme && preferences.widget.themeToggle" />
  </div>
</template>
