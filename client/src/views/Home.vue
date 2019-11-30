<template lang='pug'>
#Home
    .main-pane
        cpu-monitor
</template>

<script lang='ts'>
import { Component, Vue } from 'vue-property-decorator';
import '@/components/monitor/CpuMonitor.vue';

@Component
export default class Home extends Vue {}
</script>

<style lang='stylus'>
@require '~@/assets/styles/entry/view.styl';

html
    scroll-view: true;
    // static-view: true;

#Home
    .main-pane
        main-pane();
</style>
