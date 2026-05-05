<script setup lang="ts">
import type { Recordable } from '@vben/types';

import type { VbenFormSchema } from '@vben-core/form-ui';

import type { AuthenticationProps } from './types';

import { computed, onMounted, reactive, ref } from 'vue';
import { useRouter } from 'vue-router';

import { $t } from '@vben/locales';

import { useVbenForm } from '@vben-core/form-ui';
import { VbenButton, VbenCheckbox } from '@vben-core/shadcn-ui';

import ThirdPartyLogin from './third-party-login.vue';

interface Props extends AuthenticationProps {
  formSchema?: VbenFormSchema[];
}

defineOptions({
  name: 'AuthenticationLogin',
});

const props = withDefaults(defineProps<Props>(), {
  codeLoginPath: '/auth/code-login',
  forgetPasswordPath: '/auth/forget-password',
  formSchema: () => [],
  loading: false,
  qrCodeLoginPath: '/auth/qrcode-login',
  registerPath: '/auth/register',
  showCodeLogin: false,
  showForgetPassword: true,
  showQrcodeLogin: false,
  showRegister: true,
  showRememberMe: false,
  showThirdPartyLogin: false,
  submitButtonText: '',
  subTitle: '',
  title: '',
});

const emit = defineEmits<{
  submit: [Recordable<any>];
}>();

const [Form, formApi] = useVbenForm(
  reactive({
    commonConfig: {
      controlClass: 'login-form-control',
      formItemClass: 'login-form-item',
      hideLabel: false,
      hideRequiredMark: true,
      labelClass: 'login-form-label',
    },
    layout: 'vertical',
    schema: computed(() => props.formSchema),
    showDefaultActions: false,
    wrapperClass: 'grid-cols-1 gap-y-0',
  }),
);
const router = useRouter();

const REMEMBER_ME_KEY = `REMEMBER_ME_USERNAME_${location.hostname}`;

const localUsername = localStorage.getItem(REMEMBER_ME_KEY) || '';

const rememberMe = ref(!!localUsername);

async function handleSubmit() {
  const { valid } = await formApi.validate();
  const values = await formApi.getValues();
  if (valid) {
    localStorage.setItem(
      REMEMBER_ME_KEY,
      rememberMe.value ? values?.username : '',
    );
    emit('submit', values);
  }
}

function handleGo(path: string) {
  router.push(path);
}

onMounted(() => {
  if (localUsername) {
    formApi.setFieldValue('username', localUsername);
  }
});

defineExpose({
  getFormApi: () => formApi,
});
</script>

<template>
  <div class="login-reference" @keydown.enter.prevent="handleSubmit">
    <div class="login-reference__card">
      <div class="login-reference__orb login-reference__orb--primary"></div>
      <div class="login-reference__orb login-reference__orb--accent"></div>

      <header class="login-reference__header">
        <div class="login-reference__header-main">
          <div class="login-reference__eyebrow">
            <slot name="title">
              {{ title || $t('authentication.welcomeBack') }}
            </slot>
          </div>
          <h1 class="login-reference__title">
            {{ $t('common.login') }}
          </h1>
          <p class="login-reference__desc">
            <slot name="subTitle">
              {{ subTitle || $t('authentication.loginSubtitle') }}
            </slot>
          </p>
        </div>

        <div v-if="showRegister" class="login-reference__register">
          <span class="login-reference__register-tip">
            {{ $t('authentication.accountTip') }}
          </span>
          <span
            class="login-reference__register-link"
            @click="handleGo(registerPath)"
          >
            {{ $t('authentication.createAccount') }}
          </span>
        </div>
      </header>

      <div class="login-reference__form">
        <Form />
      </div>

      <div
        v-if="showRememberMe || showForgetPassword"
        class="login-reference__actions"
        :class="{ 'justify-end': !showRememberMe }"
      >
        <VbenCheckbox
          v-if="showRememberMe"
          v-model="rememberMe"
          class="login-reference__remember"
          name="rememberMe"
        >
          {{ $t('authentication.rememberMe') }}
        </VbenCheckbox>

        <span
          v-if="showForgetPassword"
          class="login-reference__link"
          @click="handleGo(forgetPasswordPath)"
        >
          {{ $t('authentication.forgetPassword') }}
        </span>
      </div>

      <VbenButton
        :class="{
          'cursor-wait': loading,
        }"
        :loading="loading"
        aria-label="login"
        class="login-reference__submit w-full"
        @click="handleSubmit"
      >
        <slot name="submitButtonText">
          {{ submitButtonText || $t('common.login') }}
        </slot>
      </VbenButton>

      <div
        v-if="showCodeLogin || showQrcodeLogin"
        class="login-reference__switchers"
      >
        <VbenButton
          v-if="showCodeLogin"
          class="login-reference__switcher"
          variant="outline"
          @click="handleGo(codeLoginPath)"
        >
          {{ $t('authentication.mobileLogin') }}
        </VbenButton>
        <VbenButton
          v-if="showQrcodeLogin"
          class="login-reference__switcher"
          variant="outline"
          @click="handleGo(qrCodeLoginPath)"
        >
          {{ $t('authentication.qrcodeLogin') }}
        </VbenButton>
      </div>

      <slot name="third-party-login">
        <div v-if="showThirdPartyLogin" class="login-reference__third-party">
          <ThirdPartyLogin />
        </div>
      </slot>

      <slot name="to-register"></slot>
    </div>
  </div>
</template>

<style scoped>
.login-reference {
  position: relative;
  width: 100%;
}

.login-reference__card {
  position: relative;
  padding: 28px 22px 24px;
  overflow: hidden;
  background: linear-gradient(180deg, rgb(30 41 59 / 96%), rgb(15 23 42 / 98%));
  border: 1px solid rgb(255 255 255 / 8%);
  border-radius: 32px;
  box-shadow:
    0 28px 80px rgb(15 23 42 / 50%),
    inset 0 1px 0 rgb(255 255 255 / 4%);
}

.login-reference__orb {
  position: absolute;
  pointer-events: none;
  border-radius: 9999px;
  filter: blur(16px);
}

.login-reference__orb--primary {
  top: -28px;
  right: -18px;
  width: 144px;
  height: 144px;
  background: rgb(14 165 233 / 18%);
}

.login-reference__orb--accent {
  bottom: -72px;
  left: -40px;
  width: 180px;
  height: 180px;
  background: rgb(59 130 246 / 12%);
}

.login-reference__header {
  position: relative;
  z-index: 1;
  display: flex;
  flex-direction: column;
  gap: 20px;
  margin-bottom: 26px;
}

.login-reference__header-main {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.login-reference__eyebrow {
  display: inline-flex;
  gap: 10px;
  align-items: center;
  font-size: 14px;
  font-weight: 600;
  line-height: 1.4;
  color: rgb(226 232 240 / 88%);
}

.login-reference__eyebrow::before {
  width: 10px;
  height: 10px;
  content: '';
  background: linear-gradient(135deg, #38bdf8, #2563eb);
  border-radius: 9999px;
  box-shadow: 0 0 18px rgb(56 189 248 / 55%);
}

.login-reference__title {
  margin: 0;
  font-size: clamp(48px, 7vw, 66px);
  font-weight: 800;
  line-height: 0.92;
  color: #fff;
  letter-spacing: -0.08em;
}

.login-reference__desc {
  max-width: 360px;
  margin: 0;
  font-size: 14px;
  line-height: 1.8;
  color: rgb(148 163 184 / 92%);
}

.login-reference__register {
  display: flex;
  gap: 6px;
  align-items: center;
  font-size: 14px;
  line-height: 1.5;
  color: rgb(148 163 184 / 86%);
}

.login-reference__register-link,
.login-reference__link {
  color: #1da1f2;
  cursor: pointer;
  transition: color 0.2s ease;
}

.login-reference__register-link:hover,
.login-reference__link:hover {
  color: #4cc3ff;
}

.login-reference__form {
  position: relative;
  z-index: 1;
}

.login-reference__actions {
  position: relative;
  z-index: 1;
  display: flex;
  gap: 12px;
  align-items: center;
  justify-content: space-between;
  margin: 6px 0 24px;
  font-size: 14px;
  color: rgb(191 219 254 / 90%);
}

.login-reference__remember {
  color: rgb(226 232 240 / 82%);
}

.login-reference__submit {
  position: relative;
  z-index: 1;
  height: 52px;
  font-size: 16px;
  font-weight: 700;
  color: #fff;
  background: linear-gradient(135deg, #1f9dff, #1472f4);
  border: 0;
  border-radius: 16px;
  box-shadow:
    0 14px 28px rgb(20 114 244 / 26%),
    0 0 0 1px rgb(255 255 255 / 4%) inset;
}

.login-reference__submit:hover {
  filter: brightness(1.04);
}

.login-reference__switchers {
  position: relative;
  z-index: 1;
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 12px;
  margin-top: 18px;
}

.login-reference__switcher {
  min-width: 0;
  color: rgb(226 232 240 / 90%);
  background: rgb(51 65 85 / 35%);
  border-color: rgb(148 163 184 / 20%);
  border-radius: 14px;
}

.login-reference__third-party {
  position: relative;
  z-index: 1;
}

.login-reference__form :deep(.login-form-item) {
  padding-bottom: 22px;
}

.login-reference__form :deep(.login-form-label) {
  margin-bottom: 10px;
  font-size: 15px;
  font-weight: 700;
  line-height: 1.4;
  color: rgb(241 245 249);
}

.login-reference__form :deep(.login-form-control input),
.login-reference__form :deep(.login-form-control button[role='combobox']),
.login-reference__form :deep(.login-form-control textarea) {
  min-height: 56px;
  font-size: 15px;
  color: #fff;
  background: rgb(51 65 85 / 78%);
  border: 1px solid rgb(255 255 255 / 8%);
  border-radius: 16px;
  transition:
    border-color 0.2s ease,
    box-shadow 0.2s ease,
    background-color 0.2s ease;
}

.login-reference__form :deep(.login-form-control input::placeholder),
.login-reference__form :deep(.login-form-control textarea::placeholder) {
  color: rgb(148 163 184 / 72%);
}

.login-reference__form :deep(.login-form-control input:focus-visible),
.login-reference__form :deep(.login-form-control button[role='combobox']:focus),
.login-reference__form :deep(.login-form-control textarea:focus-visible) {
  border-color: rgb(29 161 242 / 92%);
  box-shadow: 0 0 0 4px rgb(29 161 242 / 14%);
}

.login-reference__form :deep(.login-form-control svg) {
  color: rgb(148 163 184 / 78%);
}

.login-reference__form :deep(.login-form-item p[id^='form-message']) {
  position: static;
  margin-top: 8px;
  color: #fca5a5;
}

.login-reference__form :deep(.form-valid-error .login-form-control input),
.login-reference__form
  :deep(.form-valid-error .login-form-control button[role='combobox']),
.login-reference__form :deep(.form-valid-error .login-form-control textarea) {
  border-color: rgb(248 113 113 / 82%);
  box-shadow: 0 0 0 4px rgb(248 113 113 / 10%);
}

.login-reference__form
  :deep(.login-form-control > .relative > .absolute.text-foreground) {
  color: rgb(148 163 184 / 82%);
}

@media (min-width: 768px) {
  .login-reference__card {
    padding: 36px 32px 32px;
  }

  .login-reference__header {
    flex-direction: row;
    align-items: flex-start;
    justify-content: space-between;
  }

  .login-reference__register {
    justify-content: flex-end;
    padding-top: 8px;
    white-space: nowrap;
  }
}

@media (max-width: 640px) {
  .login-reference__card {
    padding: 24px 18px 22px;
    border-radius: 28px;
  }

  .login-reference__switchers {
    grid-template-columns: 1fr;
  }
}
</style>
