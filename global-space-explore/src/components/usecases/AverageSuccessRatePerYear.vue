<template>
    <O_LineChartComponent :series="series" :labels="labels" />
</template>

<script setup>
    import { ref, onMounted } from "vue"
    import { readCsv } from "@/utils/data_prepare"
    import O_LineChartComponent from "@/components/organisms/O_LineChartComponent.vue"

    const series = ref([])
    const labels = ref([])

    onMounted(async () => {
        const filePath = "/src/assets/Global_Space_Exploration_Dataset.csv"
        try {
            const data_raw = await readCsv(filePath, ['Year','Success Rate (%)'])
            const year_success_rate = {}

            data_raw['Year'].forEach((year, index) => {
                const rate = parseFloat(data_raw['Success Rate (%)'][index])

                if (!isNaN(rate)) {
                    if (!year_success_rate[year]) {
                        year_success_rate[year] = {
                            total: 0,
                            count: 0
                        };
                    }

                    year_success_rate[year].total += rate
                    year_success_rate[year].count += 1
                }
            });

            const grouped_data = Object.entries(year_success_rate).map(([year, { total, count }]) => {
                const avg = total / count
                return { context: year, average: parseFloat(avg.toFixed(2)) }
            });

            grouped_data.sort((a, b) => parseInt(a.context) - parseInt(b.context))

            labels.value = grouped_data.map(d => d.context);
            series.value = [{ name: "Average Success Rate (%)", data: grouped_data.map(d => d.average) }]
        } catch (error) {
            console.error("Failed to load CSV:", error)
        }
    })
</script>