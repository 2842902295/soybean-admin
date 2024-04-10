<script lang="ts" setup>
import {computed} from 'vue';
import {createReusableTemplate} from '@vueuse/core';
import {transformColorWithOpacity} from '@sa/utils';
import {useAppStore} from '@/store/modules/app';
import {useRouteStore} from '@/store/modules/route';
import {useThemeStore} from '@/store/modules/theme';

defineOptions({
  name: 'FirstLevelMenuReverse'
});

interface Props {
  activeMenuKey?: string;
  inverted?: boolean;
}

defineProps<Props>();

interface Emits {
  (e: 'select', menu: App.Global.Menu): boolean;
}

const emit = defineEmits<Emits>();

const appStore = useAppStore();
const themeStore = useThemeStore();
const routeStore = useRouteStore();

interface MixMenuItemProps {
  /** Menu item label */
  label: App.Global.Menu['label'];
  /** Menu item icon */
  icon: App.Global.Menu['icon'];
  /** Active menu item */
  active: boolean;
  /** Mini size */
  isMini: boolean;
}

const [DefineMixMenuItem, MixMenuItem] = createReusableTemplate<MixMenuItemProps>();

const selectedBgColor = computed(() => {
  const {darkMode, themeColor} = themeStore;

  const light = transformColorWithOpacity(themeColor, 0.1, '#ffffff');
  const dark = transformColorWithOpacity(themeColor, 0.3, '#000000');

  return darkMode ? dark : light;
});

function handleClickMixMenu(menu: App.Global.Menu) {
  emit('select', menu);
}
</script>

<template>
  <!-- 定义组件：MixMenuItem -->
  <DefineMixMenuItem v-slot="{ label, active }">
    <div
      :class="{
        'text-primary selected-mix-menu': active,
        'text-white:65 hover:text-white': inverted,
        '!text-white !bg-primary': active && inverted
      }"
      class="inline-block h-full w-100px flex-col-center cursor-pointer bg-transparent transition-300 hover:bg-[rgb(0,0,0,0.08)]"
      style="padding: 18px 0"
    >
      <!--      <component :is="icon" :class="[isMini ? 'text-icon-small' : 'text-icon-large']" />-->
      <p class="w-full ellipsis-text text-center text-15px transition-height-300">
        {{ label }}
      </p>
    </div>
  </DefineMixMenuItem>
  <!-- 定义组件端：MixMenuItem -->

  <div class="h-full flex-col-stretch flex-1-hidden">
    <slot></slot>
    <div>
      <MixMenuItem
        v-for="menu in routeStore.menus"
        :key="menu.key"
        :active="menu.key === activeMenuKey"
        :icon="menu.icon"
        :is-mini="appStore.siderCollapse"
        :label="menu.label"
        @click="handleClickMixMenu(menu)"
      />
    </div>
  </div>
</template>

<style scoped>
.selected-mix-menu {
  background-color: v-bind(selectedBgColor);
}
</style>
