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
            const data_raw = await readCsv(filePath, ['Year', 'Budget (in Billion $)'])

            const generationSums = {}
            for (let i = 0; i < data_raw['Year'].length; i++) {
                const year = data_raw['Year'][i]
                const budgetStr = data_raw['Budget (in Billion $)'][i]
                const budget = parseFloat(budgetStr) || 0

                generationSums[year] = (generationSums[year] || 0) + budget
            }

            const groupedData = Object.entries(generationSums).map(([context, total]) => ({ context, total:Number(total.toFixed(2)) }))

            labels.value = groupedData.map(d => d.context)
            series.value = [{ name: "Total Budget", data: groupedData.map(d => d.total) }]
        } catch (error) {
            console.error("Failed to load CSV:", error)
        }
    })
</script>