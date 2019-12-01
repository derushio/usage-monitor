<template lang='pug'>
#Home
    .main-pane
        cpu-monitor.q-mb-md
        q-separator.q-mb-md(inset)
</template>

<script lang='ts'>
import { Component, Vue } from 'vue-property-decorator';
import {
    QSeparator,
} from 'quasar';

import '@/components/monitor/CpuMonitor.vue';

@Component({
    components: {
        QSeparator,
    },
})
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
