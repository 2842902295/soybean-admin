<script lang="ts" setup>
import {useRouterPush} from '@/hooks/common/router';
import FirstLevelMenuReverse from '@/layouts/modules/global-menu/first-level-menu-reverse.vue';
import {useMixMenuContext} from '../../context';

defineOptions({
  name: 'HorizontalMixMenu'
});

const {activeFirstLevelMenuKey, setActiveFirstLevelMenuKey} = useMixMenuContext();
const {routerPushByKey} = useRouterPush();

function handleSelectMixMenu(menu: App.Global.Menu) {
  setActiveFirstLevelMenuKey(menu.key);

  if (!menu.children?.length) {
    routerPushByKey(menu.routeKey);
  }
}
</script>

<template>
  <FirstLevelMenuReverse :active-menu-key="activeFirstLevelMenuKey" @select="handleSelectMixMenu">
    <slot></slot>
  </FirstLevelMenuReverse>
</template>

<style scoped></style>
