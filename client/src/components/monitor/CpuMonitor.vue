<template lang='pug'>
.cpu-monitor
</template>

<script lang='ts'>
import { Component, Vue } from 'vue-property-decorator';

@Component
export default class CpuMonitor extends Vue {}
Vue.component('CpuMonitor', CpuMonitor);
</script>

<style lang='stylus' scoped>
@require '~@/assets/styles/entry/variable.styl';

.cpu-monitor {}
</style>
