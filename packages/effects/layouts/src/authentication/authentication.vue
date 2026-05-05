<script setup lang="ts">
import type { ToolbarType } from './types';

import { computed } from 'vue';

import { $t } from '@vben/locales';
import { preferences, usePreferences } from '@vben/preferences';

import { Copyright } from '../basic/copyright';
import loginRocket from './assets/login-rocket.webp';
import AuthenticationFormView from './form.vue';
import SloganIcon from './icons/slogan.vue';
import Toolbar from './toolbar.vue';

interface Props {
  appName?: string;
  logo?: string;
  logoDark?: string;
  pageTitle?: string;
  pageDescription?: string;
  sloganImage?: string;
  toolbar?: boolean;
  copyright?: boolean;
  toolbarList?: ToolbarType[];
  clickLogo?: () => void;
}

const props = withDefaults(defineProps<Props>(), {
  appName: '',
  copyright: true,
  logo: '',
  logoDark: '',
  pageDescription: '',
  pageTitle: '',
  sloganImage: '',
  toolbar: true,
  toolbarList: () => ['color', 'language', 'layout', 'theme'],
  clickLogo: () => {},
});

const { authPanelCenter, authPanelLeft, authPanelRight, isDark } =
  usePreferences();

/**
 * @zh_CN 根据主题选择合适的 logo 图标
 */
const logoSrc = computed(() => {
  // 如果是暗色主题且提供了 logoDark，则使用暗色主题的 logo
  if (isDark.value && props.logoDark) {
    return props.logoDark;
  }
  // 否则使用默认的 logo
  return props.logo;
});

const showReferenceSky = computed(
  () => authPanelRight.value && !authPanelCenter.value,
);

const sideBrandName = computed(() => props.appName || props.pageTitle);
</script>

<template>
  <div
    :class="[isDark ? 'dark' : '']"
    class="flex min-h-full flex-1 overflow-x-hidden select-none"
  >
    <template v-if="toolbar">
      <slot name="toolbar">
        <Toolbar
          :placement="showReferenceSky ? 'bottom-right' : 'top-right'"
          :toolbar-list="toolbarList"
        />
      </slot>
    </template>
    <template v-if="showReferenceSky">
      <div
        class="auth-sky relative min-h-screen w-full overflow-x-hidden overflow-y-auto bg-gray-100 transition-colors dark:bg-gray-900"
      >
        <div class="auth-sky__fill fixed top-0 left-0 z-0 w-full"></div>

        <div
          class="auth-sky__clouds fixed top-0 left-0 z-[5] w-full overflow-hidden pointer-events-none"
        >
          <div class="auth-sky__cloud auth-sky__cloud--1">
            <svg viewBox="0 0 140 60" fill="white">
              <ellipse cx="35" cy="42" rx="28" ry="16" />
              <ellipse cx="65" cy="32" rx="32" ry="22" />
              <ellipse cx="100" cy="38" rx="26" ry="18" />
              <ellipse cx="50" cy="48" rx="20" ry="10" />
              <ellipse cx="85" cy="46" rx="22" ry="12" />
            </svg>
          </div>
          <div class="auth-sky__cloud auth-sky__cloud--2">
            <svg viewBox="0 0 100 30" fill="white">
              <ellipse cx="20" cy="20" rx="18" ry="10" />
              <ellipse cx="45" cy="16" rx="20" ry="12" />
              <ellipse cx="75" cy="18" rx="22" ry="11" />
              <ellipse cx="50" cy="22" rx="30" ry="8" />
            </svg>
          </div>
          <div class="auth-sky__cloud auth-sky__cloud--3">
            <svg viewBox="0 0 55 35" fill="white">
              <ellipse cx="28" cy="18" rx="22" ry="16" />
              <ellipse cx="18" cy="24" rx="14" ry="10" />
              <ellipse cx="38" cy="24" rx="14" ry="10" />
            </svg>
          </div>
          <div class="auth-sky__cloud auth-sky__cloud--4">
            <svg viewBox="0 0 120 55" fill="white">
              <ellipse cx="30" cy="38" rx="24" ry="14" />
              <ellipse cx="55" cy="28" rx="28" ry="20" />
              <ellipse cx="85" cy="32" rx="22" ry="16" />
              <ellipse cx="45" cy="42" rx="18" ry="10" />
              <ellipse cx="70" cy="44" rx="20" ry="9" />
              <ellipse cx="60" cy="22" rx="15" ry="12" />
            </svg>
          </div>
          <div class="auth-sky__cloud auth-sky__cloud--5">
            <svg viewBox="0 0 45 22" fill="white">
              <ellipse cx="15" cy="14" rx="12" ry="8" />
              <ellipse cx="30" cy="12" rx="13" ry="9" />
            </svg>
          </div>
          <div class="auth-sky__cloud auth-sky__cloud--6">
            <svg viewBox="0 0 90 35" fill="white">
              <ellipse cx="22" cy="22" rx="18" ry="12" />
              <ellipse cx="50" cy="18" rx="22" ry="14" />
              <ellipse cx="72" cy="24" rx="16" ry="10" />
              <ellipse cx="38" cy="26" rx="14" ry="8" />
            </svg>
          </div>
          <div class="auth-sky__cloud auth-sky__cloud--7">
            <svg viewBox="0 0 70 45" fill="white">
              <ellipse cx="35" cy="25" rx="30" ry="18" />
              <ellipse cx="22" cy="32" rx="18" ry="12" />
              <ellipse cx="48" cy="32" rx="18" ry="12" />
              <ellipse cx="35" cy="18" rx="16" ry="12" />
            </svg>
          </div>
        </div>

        <slot name="logo">
          <div
            v-if="logoSrc || appName"
            class="auth-sky__logo fixed z-20"
            @click="clickLogo"
          >
            <img
              v-if="logoSrc"
              :key="logoSrc"
              :alt="appName"
              :src="logoSrc"
              class="auth-sky__logo-image"
            />
            <p v-else class="auth-sky__logo-text">
              {{ appName }}
            </p>
          </div>
        </slot>

        <div class="auth-sky__rocket hidden xl:block">
          <img
            :alt="appName"
            :src="loginRocket"
            class="auth-sky__rocket-image"
          />
        </div>

        <div class="auth-sky__copy hidden xl:block">
          <h2 class="auth-sky__copy-title">
            {{ $t('common.login') }}
          </h2>
          <p class="auth-sky__copy-brand">
            {{ sideBrandName }}
          </p>
          <p class="auth-sky__copy-desc">
            {{ $t('authentication.loginPageLead') }}
          </p>
        </div>

        <div
          class="relative z-20 flex justify-center px-4 pt-14 pb-3 xl:hidden"
        >
          <img
            :alt="appName"
            :src="loginRocket"
            class="h-[160px] w-[160px] object-contain auth-sky__rocket-image"
          />
        </div>

        <AuthenticationFormView class="auth-sky__panel" data-side="right">
          <template v-if="copyright" #copyright>
            <slot name="copyright">
              <Copyright
                v-if="preferences.copyright.enable"
                v-bind="preferences.copyright"
              />
            </slot>
          </template>
        </AuthenticationFormView>
      </div>
    </template>
    <!-- 左侧认证面板 -->
    <AuthenticationFormView
      v-else-if="authPanelLeft"
      class="min-h-full w-2/5 flex-1"
      data-side="left"
    >
      <template v-if="copyright" #copyright>
        <slot name="copyright">
          <Copyright
            v-if="preferences.copyright.enable"
            v-bind="preferences.copyright"
          />
        </slot>
      </template>
    </AuthenticationFormView>

    <slot v-if="!showReferenceSky" name="logo">
      <!-- 头部 Logo 和应用名称 -->
      <div
        v-if="logoSrc || appName"
        class="absolute top-0 left-0 z-10 flex flex-1"
        @click="clickLogo"
      >
        <div
          class="mt-4 ml-4 flex flex-1 items-center text-foreground sm:top-6 sm:left-6 lg:text-foreground"
        >
          <img
            v-if="logoSrc"
            :key="logoSrc"
            :alt="appName"
            :src="logoSrc"
            class="mr-2"
            width="42"
          />
          <p v-if="appName" class="m-0 text-xl font-medium">
            {{ appName }}
          </p>
        </div>
      </div>
    </slot>

    <!-- 系统介绍 -->
    <div
      v-if="!showReferenceSky && !authPanelCenter"
      class="relative hidden w-0 flex-1 lg:block"
    >
      <div
        class="absolute inset-0 size-full bg-background-deep dark:bg-[#070709]"
      >
        <div class="login-background absolute top-0 left-0 size-full"></div>
        <div
          :key="authPanelLeft ? 'left' : authPanelRight ? 'right' : 'center'"
          class="mr-20 flex-col-center h-full"
          :class="{
            'enter-x': authPanelLeft,
            '-enter-x': authPanelRight,
          }"
        >
          <template v-if="sloganImage">
            <img
              :alt="appName"
              :src="sloganImage"
              class="h-64 w-2/5 animate-float"
            />
          </template>
          <SloganIcon v-else :alt="appName" class="h-64 w-2/5 animate-float" />
          <div class="text-1xl mt-6 font-sans text-foreground lg:text-2xl">
            {{ pageTitle }}
          </div>
          <div class="mt-2 dark:text-muted-foreground">
            {{ pageDescription }}
          </div>
        </div>
      </div>
    </div>

    <!-- 中心认证面板 -->
    <div
      v-if="!showReferenceSky && authPanelCenter"
      class="relative flex-center w-full"
    >
      <div class="login-background absolute top-0 left-0 size-full"></div>
      <AuthenticationFormView
        class="w-full rounded-3xl pb-20 shadow-float shadow-primary/5 md:w-2/3 md:bg-background lg:w-1/2 xl:w-[36%]"
        data-side="bottom"
      >
        <template v-if="copyright" #copyright>
          <slot name="copyright">
            <Copyright
              v-if="preferences.copyright.enable"
              v-bind="preferences.copyright"
            />
          </slot>
        </template>
      </AuthenticationFormView>
    </div>

    <!-- 右侧认证面板 -->
    <AuthenticationFormView
      v-if="!showReferenceSky && authPanelRight"
      class="min-h-full w-2/5 flex-1"
      data-side="right"
    >
      <template v-if="copyright" #copyright>
        <slot name="copyright">
          <Copyright
            v-if="preferences.copyright.enable"
            v-bind="preferences.copyright"
          />
        </slot>
      </template>
    </AuthenticationFormView>
  </div>
</template>

<style scoped>
.auth-sky__fill {
  height: 100vh;
  background-color: rgb(0 137 237);
}

.auth-sky__clouds {
  height: 100%;
}

.auth-sky__rocket {
  position: absolute;
  top: 25px;
  left: 349px;
  z-index: 10;
  width: 485px;
  height: 485px;
}

.auth-sky__rocket-image {
  position: relative;
  z-index: 10;
  width: 100%;
  height: 100%;
  object-fit: contain;
  animation: auth-sky-rocket-float 3s ease-in-out infinite;
}

.auth-sky__logo {
  top: 1rem;
  left: 1rem;
}

.auth-sky__logo-image {
  width: auto;
  max-width: 180px;
  height: 2rem;
  object-fit: contain;
}

.auth-sky__logo-text {
  margin: 0;
  font-size: 1.125rem;
  font-weight: 700;
  color: #fff;
}

.auth-sky__copy {
  position: absolute;
  top: 150px;
  left: 73px;
  z-index: 10;
  width: 396px;
  color: #fff;
}

.auth-sky__copy-title {
  margin: 0;
  font-size: 34px;
  font-weight: 600;
  line-height: 51px;
}

.auth-sky__copy-brand {
  margin: 0;
  font-size: 24px;
  line-height: 36px;
}

.auth-sky__copy-desc {
  max-width: 311px;
  margin: 22px 0 0;
  font-size: 13px;
  font-weight: 300;
  line-height: 19px;
}

.auth-sky__panel {
  position: relative;
  z-index: 20;
  min-height: 100vh;
  padding-bottom: 60px;
}

.auth-sky__cloud {
  position: absolute;
  left: 0;
  filter: drop-shadow(0 2px 4px rgb(255 255 255 / 30%));
  transform: translate(-200px);
  animation: auth-sky-cloud-drift linear infinite;
  animation-fill-mode: backwards;
}

.auth-sky__cloud svg {
  width: 100%;
  height: 100%;
}

.auth-sky__cloud--1 {
  top: 6%;
  width: 140px;
  height: 60px;
  opacity: 0.85;
  animation-duration: 22s;
}

.auth-sky__cloud--2 {
  top: 18%;
  width: 100px;
  height: 30px;
  opacity: 0.6;
  animation-duration: 18s;
  animation-delay: 4s;
}

.auth-sky__cloud--3 {
  top: 32%;
  width: 55px;
  height: 35px;
  opacity: 0.5;
  animation-duration: 15s;
  animation-delay: 1s;
}

.auth-sky__cloud--4 {
  top: 48%;
  width: 120px;
  height: 55px;
  opacity: 0.75;
  animation-duration: 24s;
  animation-delay: 8s;
}

.auth-sky__cloud--5 {
  top: 62%;
  width: 45px;
  height: 22px;
  opacity: 0.45;
  animation-duration: 16s;
  animation-delay: 12s;
}

.auth-sky__cloud--6 {
  top: 25%;
  width: 90px;
  height: 35px;
  opacity: 0.55;
  animation-duration: 20s;
  animation-delay: 6s;
}

.auth-sky__cloud--7 {
  top: 72%;
  width: 70px;
  height: 45px;
  opacity: 0.65;
  animation-duration: 19s;
  animation-delay: 3s;
}

@keyframes auth-sky-cloud-drift {
  0% {
    transform: translate(-200px);
  }

  100% {
    transform: translate(calc(100vw + 200px));
  }
}

@keyframes auth-sky-rocket-float {
  0%,
  100% {
    transform: translateY(0);
  }

  50% {
    transform: translateY(-12px);
  }
}

.login-background {
  background: linear-gradient(
    154deg,
    #07070915 30%,
    hsl(var(--primary) / 30%) 48%,
    #07070915 64%
  );
  filter: blur(100px);
}

.dark {
  .login-background {
    background: linear-gradient(
      154deg,
      #07070915 30%,
      hsl(var(--primary) / 20%) 48%,
      #07070915 64%
    );
    filter: blur(100px);
  }
}

@media (min-width: 1024px) {
  .auth-sky__fill {
    height: 50vh;
  }

  .auth-sky__clouds {
    height: 50vh;
  }

  .auth-sky__logo {
    top: 31px;
    left: 42px;
  }

  .auth-sky__panel {
    position: absolute;
    top: 50%;
    right: 60px;
    min-height: auto;
    padding-bottom: 0;
    transform: translateY(-50%);
  }
}

@media (min-width: 1280px) {
  .auth-sky__panel {
    right: 118px;
  }
}
</style>
