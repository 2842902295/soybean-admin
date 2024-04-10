<script setup lang="ts">
import {computed} from 'vue';
import {useFullscreen} from '@vueuse/core';
import {useAppStore} from '@/store/modules/app';
import {useThemeStore} from '@/store/modules/theme';
import {useRouteStore} from '@/store/modules/route';
import HorizontalMixMenuReverse from '@/layouts/modules/global-menu/horizontal-mix-menu-reverse.vue';
import HorizontalMenu from '../global-menu/base-menu.vue';
import GlobalLogo from '../global-logo/index.vue';
import GlobalBreadcrumb from '../global-breadcrumb/index.vue';
import {useMixMenuContext} from '../../context';
import ThemeButton from './components/theme-button.vue';
import UserAvatar from './components/user-avatar.vue';

defineOptions({
  name: 'GlobalHeader'
});

interface Props {
  /** Whether to show the logo */
  showLogo?: App.Global.HeaderProps['showLogo'];
  /** Whether to show the menu toggler */
  showMenuToggler?: App.Global.HeaderProps['showMenuToggler'];
  /** Whether to show the menu */
  showMenu?: App.Global.HeaderProps['showMenu'];
  reverse?: App.Global.HeaderProps['reverse'];
}

defineProps<Props>();

const appStore = useAppStore();
const themeStore = useThemeStore();
const routeStore = useRouteStore();
const { isFullscreen, toggle } = useFullscreen();
const { menus } = useMixMenuContext();

const headerMenus = computed(() => {
  if (themeStore.layout.mode === 'horizontal') {
    return routeStore.menus;
  }

  // 关键代码
  if (themeStore.layout.mode === 'horizontal-mix') {
    return menus.value;
  }

  if (themeStore.layout.mode === 'horizontal-mix-reverse') {
    return routeStore.menus;
  }

  return [];
});
</script>

<template>
  <DarkModeContainer class="h-full flex-y-center shadow-header">
    <GlobalLogo v-if="showLogo" class="h-full" :style="{ width: themeStore.sider.width + 'px' }" />
    <MenuToggler v-if="reverse" :collapsed="appStore.siderCollapse" @click="appStore.toggleSiderCollapse"/>
    <HorizontalMixMenuReverse v-if="reverse"/>
    <HorizontalMenu v-else-if="showMenu" :menus="headerMenus" class="px-12px" mode="horizontal"/>
    <div v-else class="h-full flex-y-center flex-1-hidden">
      <MenuToggler v-if="showMenuToggler" :collapsed="appStore.siderCollapse" @click="appStore.toggleSiderCollapse" />
      <GlobalBreadcrumb v-if="!appStore.isMobile" class="ml-12px" />
    </div>
    <div class="h-full flex-y-center justify-end">
      <!--      <GlobalSearch />-->
      <FullScreen v-if="!appStore.isMobile" :full="isFullscreen" @click="toggle" />
      <LangSwitch :lang="appStore.locale" :lang-options="appStore.localeOptions" @change-lang="appStore.changeLocale" />
      <ThemeSchemaSwitch
        :theme-schema="themeStore.themeScheme"
        :is-dark="themeStore.darkMode"
        @switch="themeStore.toggleThemeScheme"
      />
      <ThemeButton />
      <UserAvatar />
    </div>
  </DarkModeContainer>
</template>

<style scoped></style>
