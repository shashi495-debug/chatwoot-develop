<template>
  <woot-button
    size="small"
    variant="clear"
    color-scheme="secondary"
    class="-ml-3 text-black-900 dark:text-slate-300"
    icon="list"
    @click="onMenuItemClick"
  />
</template>

<script>
import { BUS_EVENTS } from 'shared/constants/busEvents';

export default {
  methods: {
    onMenuItemClick() {
      bus.$emit(BUS_EVENTS.TOGGLE_SIDEMENU);
    },
  },
};
</script>
