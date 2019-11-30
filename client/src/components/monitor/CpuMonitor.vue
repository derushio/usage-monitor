<template lang='pug'>
.cpu-monitor
    .row
        .col-2(v-for='cpuHistory in cpuHistories')
            .q-ma-md
                line-chart(:data='parseHistory(cpuHistory)' :min='0' :max='100' :dataset='{ pointRadius: 0 }'
                    :library='{ scales: { xAxes: [ { display: false } ] } }')
</template>

<script lang='ts'>
import { Component, Vue } from 'vue-property-decorator';
import cpuStats from 'async-cpu-stats';

import ArrayUtil from '@/utils/ArrayUtil';

@Component
export default class CpuMonitor extends Vue {
    protected cpuHistories = [] as number[][];
    protected timer?: number;

    protected hisotryRecordDelay = 100;
    protected historyLength = 100;

    protected mounted() {
        this.startTimer();
    }

    protected startTimer() {
        this.timer = window.setInterval(() => {
            this.recordCpuStats();
        }, this.hisotryRecordDelay);
    }

    protected stopTimer() {
        if (!!this.timer) {
            window.clearInterval(this.timer);
        }
    }

    protected async recordCpuStats() {
        const stats = await cpuStats();

        for (const si in stats) {
            const i = Number(si);
            let cpuHistory = this.cpuHistories[i];
            if (!cpuHistory) {
                this.$set(this.cpuHistories, i, []);
                cpuHistory = this.cpuHistories[i];
            }

            cpuHistory.push(stats[i].cpu);

            if (cpuHistory.length >= this.historyLength) {
                this.$set(this.cpuHistories, i, cpuHistory.slice(-this.historyLength));
            }
        }
    }

    protected parseHistory(history: number[]) {
        const gap = this.historyLength - history.length;
        let his = history.reduce((prev, cpu, i) => {
            prev[i + gap] = cpu;
            return prev;
        }, {} as { [label: number]: number | null });

        his = ArrayUtil.range(gap).reduce((prev, v, i) => {
            prev[i] = null;
            return prev;
        }, his);

        return his;
    }
}
Vue.component('CpuMonitor', CpuMonitor);
</script>

<style lang='stylus' scoped>
@require '~@/assets/styles/entry/variable.styl';

.cpu-monitor {}
</style>
