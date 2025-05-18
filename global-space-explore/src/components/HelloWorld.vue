<script setup>
  import A_TextComponent from "@/components/atoms/A_TextComponent.vue"
  import O_TableComponent from "@/components/organisms/O_TableComponent.vue"
  import { readBody, readHeader } from "@/utils/data_prepare";
  import { onMounted, ref } from "vue";
  import M_DescriptiveStatistic from "@/components/molecules/M_DescriptiveStatistic.vue"
  import ShowPieChart from "@/components/usecases/ShowPieChart.vue"
  import TotalMissionPerYear from "@/components/usecases/TotalMissionPerYear.vue"
  import TotalSpendingPerYear from "./usecases/TotalSpendingPerYear.vue";
  import TotalMissionPerGroupAndContext from "./usecases/TotalMissionPerGroupAndContext.vue";

  const header_dataset = ref([])
  const body_dataset = ref([])

  onMounted(async () => {
    const filePath = "/src/assets/Global_Space_Exploration_Dataset.csv" 
    try {
      const data_header = await readHeader(filePath)
      const data_body = await readBody(filePath)

      // Data Prepare
      const data_header_clean = data_header.filter(dt => dt !== "Unnamed: 0")
      header_dataset.value = data_header_clean.map(header => ({
        label: header,
        field: header,
        sortable: true
      }))      
      const data_body_clean = data_body.map(row => {
        return Object.fromEntries(
            Object.entries(row).filter(([key]) => data_header_clean.includes(key))
        )
      })
      body_dataset.value = data_body_clean
    } catch (error) {
      console.error("Failed to load CSV:", error)
    }
  })
</script>

<template>
  <div class="greetings">
    <A_TextComponent title="Global Space Exploration" />
    <div class="row">
      <div class="col-lg-6 col-md-6 col-sm-12 col-12">
        <A_TextComponent second_title="About" />
        <A_TextComponent content="The Global Space Exploration Dataset (2000-2024) is a meticulously curated collection that chronicles humanity's advancements in space technology and exploration over the past two decades. This dataset encompasses missions from leading space agencies, including NASA, SpaceX, ISRO, ESA, and Roscosmos. It provides in-depth insights into mission types, budgets, crew details, launch success rates, and technological innovations. With over 10,000 mission records, the dataset empowers researchers, data scientists, and space enthusiasts to analyze trends in satellite launches, deep-space missions, and reusable rocket technologies. Use this dataset for data visualization, machine learning models, and time-series forecasting, enabling deeper understanding of spacecraft technologies, ground control communication, and orbit dynamics. Whether tracking the success rate of reusable rockets or studying the contribution of different nations to space exploration, this dataset serves as a valuable resource for data-driven discoveries."/>
      </div>
      <div class="col-lg-6 col-md-6 col-sm-12 col-12">
        <A_TextComponent second_title="Source" />
        <a href="https://www.kaggle.com/datasets/atharvasoundankar/global-space-exploration-dataset-2000-2025?resource=download" target="_blank" rel="noopener">Kaggle</a>
        <a href="https://github.com/FlazeFy2/DS-Global-Space-Exploration" target="_blank" rel="noopener">Jupiter Notebook</a>
      </div>
    </div>
    
    <hr>
    <div class="d-flex justify-content-start mt-3">
      <button class="accordion-button" type="button" data-bs-toggle="collapse" data-bs-target="#collapseOne" aria-expanded="true" aria-controls="collapseOne">Dataset</button>
      <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#collapseTwo" aria-expanded="false" aria-controls="collapseTwo">Descriptive Statistic</button>
      <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#collapseThree" aria-expanded="false" aria-controls="collapseThree">Line Map</button>
      <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#collapseFour" aria-expanded="false" aria-controls="collapseFour">Pie Chart</button>
      <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#collapseFive" aria-expanded="false" aria-controls="collapseFive">Column Chart</button>
    </div>

    <div class="accordion" id="accordionExample">
      <div class="accordion-item">
        <div id="collapseOne" class="accordion-collapse collapse show" data-bs-parent="#accordionExample">
          <A_TextComponent second_title="Dataset" />
          <O_TableComponent :header="header_dataset" :body="body_dataset"/>
        </div>
      </div>

      <div class="accordion-item">
        <div id="collapseTwo" class="accordion-collapse collapse" data-bs-parent="#accordionExample">
          <A_TextComponent second_title="Descriptive Statistic" />
          <div class="row">
            <div class="col-lg-6 col-md-6 col-sm-12 col-12">
              <M_DescriptiveStatistic target_col="Budget (in Billion $)"/>
            </div>
            <div class="col-lg-6 col-md-6 col-sm-12 col-12">
              <M_DescriptiveStatistic target_col="Success Rate (%)"/>
            </div>
            <div class="col-lg-6 col-md-6 col-sm-12 col-12">
              <M_DescriptiveStatistic target_col="Duration (in Days)"/>
            </div>
          </div>
        </div>
      </div>

      <div class="accordion-item">
        <div id="collapseThree" class="accordion-collapse collapse" data-bs-parent="#accordionExample">
          <A_TextComponent third_title="Total Mission Per Year"/>
          <TotalMissionPerYear/>
          <A_TextComponent third_title="Total Budget Spending (in Billion $) Per Year"/>
          <TotalSpendingPerYear/>
        </div>
      </div>

      <div class="accordion-item">
        <div id="collapseFour" class="accordion-collapse collapse" data-bs-parent="#accordionExample">
          <A_TextComponent second_title="Line Chart" />
          <ShowPieChart/>
        </div>
      </div>

      <div class="accordion-item">
        <div id="collapseFive" class="accordion-collapse collapse" data-bs-parent="#accordionExample">
          <!-- Exploratory Data Analysis (EDA) - Stacked Bar Chart Total Mission Per Mission Type By Its Sattelite Type -->
          <A_TextComponent second_title="Column Chart" />
          <div class="row">
            <div class="col-lg-6 col-md-6 col-sm-12">
              <TotalMissionPerGroupAndContext
                second_title="Total Purchase Item Per Mission Type  By Its Satellite Type" 
                content="This chart shows the total purchase item group by its mission type  and satellite type"
                count_col="Satellite Type"
                group_col="Mission Type "
                limit=7
              />
            </div>
          </div>
          
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
  a {
    color: #42b883;
    font-weight: 500;
    font-size: 14px;
    background-color:rgba(66, 184, 131, 0.25);
    border-radius: 20px;
    padding: 7px 12px;
    margin-right: 6px;
  }
</style>
