<template>
    <O_ColumnChartComponent 
        :series="chartData" 
        :labels="chartLabels" 
        :second_title="second_title" 
        :content="content"
    />
</template>

<script setup>
    import { ref, onMounted, defineProps } from "vue"
    import O_ColumnChartComponent from "../organisms/O_ColumnChartComponent.vue"
    import { readCsv } from "@/utils/data_prepare"

    const props = defineProps({
        second_title: {
            type: String,
            default: ""
        },
        content: {
            type: String,
            default: ""
        },
        count_col: {
            type: String,
            required: true
        },
        limit: {
            type: Number,
            required: false,
            default: 7
        }
    })

    const chartData = ref([])
    const chartLabels = ref([])

    onMounted(async () => {
        const filePath = "/src/assets/Global_Space_Exploration_Dataset.csv"
        try {
            const data_raw = await readCsv(filePath, [props.count_col])

            // Count Total Apepar Of Unique Val
            const countOccurrences = data_raw[props.count_col].reduce((acc, value) => {
                acc[value] = (acc[value] || 0) + 1
                return acc
            }, {})

            // Label
            chartLabels.value = Object.entries(countOccurrences)
                .sort((a, b) => b[1] - a[1]) 
                .slice(0, props.limit)       
                .map(([label]) => label)    

            chartData.value = [{
                name: props.count_col,
                data: chartLabels.value.map(label => countOccurrences[label])
            }]
        } catch (error) {
            console.error("Failed to load CSV:", error)
        }
    })
</script>
