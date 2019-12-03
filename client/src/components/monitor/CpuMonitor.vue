<template lang='pug'>
.cpu-monitor
    h3.text-h5.q-my-xs CPUモニタ
    q-select.select(v-model='hisotryRecordDelay' @input='startTimer' :options='[ 50, 100, 500, 1000, 5000 ]' label='更新毎秒')

    h4.text-h6 CPU数: {{ cpuHistories.length }}

    h4.text-h6 総合モニタ
    .row
        .col
            .q-ma-xs
                q-card: q-card-section
                    line-chart(:data='parseTotalHistory(cpuHistories)' height='150px' :min='0' :max='100' :dataset='{ pointRadius: 0 }'
                            :library='{ scales: { xAxes: [ { display: false } ] } }')

    h4.text-h6 個別モニタ
    q-tabs(v-model='mode' align='justify')
        q-tab(name='multi' label='別表示')
        q-tab(name='single' label='統合表示')

    q-tab-panels(v-model='mode' :animated='true')
        q-tab-panel(name='multi')
            .row
                .col-2(v-for='cpuHistory, i in cpuHistories')
                    .q-ma-xs
                        q-card: q-card-section
                            h5.text-h6.q-my-xs CPU: {{ i }}
                            line-chart(:data='parseHistory(cpuHistory)' height='150px' :min='0' :max='100' :dataset='{ pointRadius: 0 }'
                                    :library='{ scales: { xAxes: [ { display: false } ] } }')

        q-tab-panel(name='single')
            .row
                .col-2(v-for='cpuHistory, i in cpuHistories')
                    .q-ma-xs
</template>

<script lang='ts'>
import { Component, Vue } from 'vue-property-decorator';
import {
    QSelect,
    QTabs, QTab, QTabPanels, QTabPanel,
    QCard, QCardSection,
} from 'quasar';

import cpuStats from 'async-cpu-stats';

import ArrayUtil from '@/utils/ArrayUtil';

@Component({
    components: {
        QSelect,
        QTabs, QTab, QTabPanels, QTabPanel,
        QCard, QCardSection,
    },
})
export default class CpuMonitor extends Vue {
    protected cpuHistories = [] as number[][];
    protected timer?: number;

    protected hisotryRecordDelay = 500;
    protected historyLength = 100;

    protected mode = 'multi';

    protected mounted() {
        this.startTimer();
    }

    protected startTimer() {
        this.stopTimer();

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

    protected parseTotalHistory(histories: number[][]) {
        if (histories[0] == null) { return {}; }

        const gap = this.historyLength - histories[0].length;
        let his = histories[0].reduce((prev, history, i) => {
            let totalCpu = 0;
            for (const index of ArrayUtil.range(histories.length)) {
                totalCpu += histories[index][i];
            }

            prev[i + gap] = (totalCpu / (this.cpuHistories.length * 100)) * 100;
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

.cpu-monitor
    .select
        width: 200px;
</style>
