<template lang='pug'>
.cpu-monitor
    .row
        .col-4(v-for='cpu in cpus')
            span.block {{ cpu }}
</template>

<script lang='ts'>
import { Component, Vue } from 'vue-property-decorator';
import cpuStats from 'async-cpu-stats';

@Component
export default class CpuMonitor extends Vue {
    protected cpus = [] as number[];
    protected timer?: number;

    protected mounted() {
        this.startTimer();
    }

    protected startTimer() {
        this.timer = window.setInterval(() => {
            this.recordCpuStats();
        }, 300);
    }

    protected stopTimer() {
        if (this.timer) {
            window.clearInterval(this.timer);
        }
    }

    protected async recordCpuStats() {
        const stats = await cpuStats();

        this.$set(this, 'cpus', []);
        for (const stat of stats) {
            this.cpus.push(stat.cpu);
        }
    }
}
Vue.component('CpuMonitor', CpuMonitor);
</script>

<style lang='stylus' scoped>
@require '~@/assets/styles/entry/variable.styl';

.cpu-monitor {}
</style>
