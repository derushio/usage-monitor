<template lang='pug'>
.cpu-analize
</template>

<script lang='ts'>
import { Component, Vue } from 'vue-property-decorator';

@Component
export default class CpuAnalize extends Vue {}
Vue.component('CpuAnalize', CpuAnalize);
</script>

<style lang='stylus' scoped>
@require '~@/assets/styles/entry/variable.styl';

.cpu-analize {}
</style>
