<script setup>
    import { ref, onMounted } from "vue"
    import { readCsv } from "@/utils/data_prepare"
    import O_PieChartComponent from "@/components/organisms/O_PieChartComponent.vue"
    import { countWordFrequencies } from "@/utils/analyze";

    const labels_mission_type_comparison = ref([])
    const series_mission_type_comparison = ref([])
    const labels_satellite_type_comparison = ref([])
    const series_satellite_type_comparison = ref([])
    const labels_environmental_impact_comparison = ref([])
    const series_environmental_impact_comparison = ref([])

    onMounted(async () => {
        const filePath = "/src/assets/Global_Space_Exploration_Dataset.csv" 
        try {
            const data_raw = await readCsv(filePath,['Mission Type','Satellite Type','Environmental Impact'])
            
            // Exploratory Data Analysis (EDA) - Pie Chart Share of Mission Type
            const mission_type_comparison = countWordFrequencies(data_raw['Mission Type'])
            labels_mission_type_comparison.value = Object.keys(mission_type_comparison)
            series_mission_type_comparison.value = Object.values(mission_type_comparison)

            // Exploratory Data Analysis (EDA) - Pie Chart Share of Satellite Type
            const satellite_type_comparison = countWordFrequencies(data_raw['Satellite Type'])
            labels_satellite_type_comparison.value = Object.keys(satellite_type_comparison)
            series_satellite_type_comparison.value = Object.values(satellite_type_comparison)

            // Exploratory Data Analysis (EDA) - Pie Chart Share of Environmental Impact
            const environmental_impact_comparison = countWordFrequencies(data_raw['Environmental Impact'])
            labels_environmental_impact_comparison.value = Object.keys(environmental_impact_comparison)
            series_environmental_impact_comparison.value = Object.values(environmental_impact_comparison)
        } catch (error) {
            console.error("Failed to load CSV:", error)
        }
    })
</script>

<template>
    <div class="row">
        <div class="col-lg-4 col-md-6 col-sm-12 col-12 mx-auto">
            <!-- Exploratory Data Analysis (EDA) - Pie Chart Mission Type Comparison -->
            <O_PieChartComponent 
                :series="series_mission_type_comparison" 
                :labels="labels_mission_type_comparison" 
                second_title="Mission Type Comparison" 
                content="This compare total mission by its mission type"
            />
        </div>
        <div class="col-lg-4 col-md-6 col-sm-12 col-12 mx-auto">
            <!-- Exploratory Data Analysis (EDA) - Pie Chart Satellite Type Comparison -->
            <O_PieChartComponent 
                :series="series_satellite_type_comparison" 
                :labels="labels_satellite_type_comparison" 
                second_title="Sattelite Type Comparison" 
                content="This compare total mission by satellite type comparison"
            />
        </div>
        <div class="col-lg-4 col-md-6 col-sm-12 col-12 mx-auto">
            <!-- Exploratory Data Analysis (EDA) - Pie Chart Environmental Impact Comparison -->
            <O_PieChartComponent 
                :series="series_environmental_impact_comparison" 
                :labels="labels_environmental_impact_comparison" 
                second_title="Environmental Impact Comparison" 
                content="This compare total mission by its environmental impact comparison"
            />
        </div>
    </div>
</template>

