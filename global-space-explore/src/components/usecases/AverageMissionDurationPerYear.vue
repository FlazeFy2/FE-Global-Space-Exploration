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
            const data_raw = await readCsv(filePath, ['Year','Duration (in Days)'])
            const year_mission_days = {}

            data_raw['Year'].forEach((year, index) => {
                const days = parseFloat(data_raw['Duration (in Days)'][index])

                if (!isNaN(days)) {
                    if (!year_mission_days[year]) {
                        year_mission_days[year] = {
                            total: 0,
                            count: 0
                        }
                    }

                    year_mission_days[year].total += days
                    year_mission_days[year].count += 1
                }
            });

            const grouped_data = Object.entries(year_mission_days).map(([year,{total, count}]) => {
                const avg = total / count 
                return { context: year, average: parseFloat(avg.toFixed(2)) }
            });

            grouped_data.sort((a, b) => parseInt(a.context) - parseInt(b.context))

            labels.value = grouped_data.map(d => d.context);
            series.value = [{ name: "Duration (in Days)", data: grouped_data.map(d => d.average) }]
        } catch (error) {
            console.error("Failed to load CSV:", error)
        }
    })
</script>