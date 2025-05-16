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
            const data_raw = await readCsv(filePath, ['Year'])

            const generationCounts = {}
            data_raw['Year'].forEach(date => {
                generationCounts[date] = (generationCounts[date] || 0) + 1
            })

            const groupedData = Object.entries(generationCounts)
                .map(([context, total]) => ({ context, total }))

            labels.value = groupedData.map(d => d.context)
            series.value = [{ name: "Total", data: groupedData.map(d => d.total) }]
        } catch (error) {
            console.error("Failed to load CSV:", error)
        }
    })
</script>