<script setup lang="ts">
import {computed} from 'vue';
import {useAppStore} from '@/store/modules/app';
import {useThemeStore} from '@/store/modules/theme';
import {useRouteStore} from '@/store/modules/route';
import HorizontalMenu from '@/layouts/modules/global-menu/base-menu.vue';
import {useMixMenuContext} from '@/layouts/context';
import GlobalLogo from '../global-logo/index.vue';
import VerticalMenu from '../global-menu/base-menu.vue';
import VerticalMixMenu from '../global-menu/vertical-mix-menu.vue';
import HorizontalMixMenu from '../global-menu/horizontal-mix-menu.vue';

defineOptions({
  name: 'GlobalSider'
});

const appStore = useAppStore();
const themeStore = useThemeStore();
const routeStore = useRouteStore();

const {menus} = useMixMenuContext();

const isVerticalMix = computed(() => themeStore.layout.mode === 'vertical-mix');
const isHorizontalMix = computed(() => themeStore.layout.mode === 'horizontal-mix');
const isHorizontalMixReverse = computed(() => themeStore.layout.mode === 'horizontal-mix-reverse');
const darkMenu = computed(() => !themeStore.darkMode && !isHorizontalMix.value && themeStore.sider.inverted);
const showLogo = computed(() => !isVerticalMix.value && !isHorizontalMix.value && !isHorizontalMixReverse.value);
</script>

<template>
  <DarkModeContainer class="size-full flex-col-stretch shadow-sider" :inverted="darkMenu">
    <GlobalLogo
      v-if="showLogo"
      :show-title="!appStore.siderCollapse"
      :style="{ height: themeStore.header.height + 'px' }"
    />
    <VerticalMixMenu v-if="isVerticalMix">
      <GlobalLogo :show-title="false" :style="{ height: themeStore.header.height + 'px' }" />
    </VerticalMixMenu>
    <HorizontalMixMenu v-else-if="isHorizontalMix" />
    <HorizontalMenu v-else-if="isHorizontalMixReverse" :menus="menus"/>
    <VerticalMenu v-else :dark-theme="darkMenu" :menus="routeStore.menus" />
  </DarkModeContainer>
</template>

<style scoped></style>
