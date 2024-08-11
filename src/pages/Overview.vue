<template>
  <div class="content">
    <div class="container-fluid">
      <div class="row">
        <div class="col-xl-3 col-md-6 align-items-center justify-content-center" v-for="(item, index) in stats" :key="index">
          <stats-card>
            <div slot="content" class=" flex-column align-items-center justify-content-center text-center">
              <p class="card-category mb-0">{{ item.category }}</p>
              <h4 class="card-title mb-0">{{ item.count }}</h4>
            </div>
            <div slot="footer"></div>
          </stats-card>
        </div>
      </div>
      <div class="row">
        <div class="col-md-4">
          <chart-card :chart-data="pieChart.data" chart-type="Pie">
            <template slot="header">
              <h4 class="card-title">Distribusi Departemen Karyawan</h4>
            </template>
          </chart-card>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
  import ChartCard from 'src/components/Cards/ChartCard.vue'
  import StatsCard from 'src/components/Cards/StatsCard.vue'
  import LTable from 'src/components/Table.vue'
  import axios from 'axios';




  export default {
  components: {
      LTable,
      ChartCard,
      StatsCard
  },
  data () {
    return {
      pieChart: {
        data: {
          labels: [],
          series: [],
          stats: []
        }
      },
      stats:[]
    }
  },
  methods: {
    async fetchChartData() {
      try {
        const response = await axios.get('http://localhost:3000/karyawan/statistik/api/v1');
        const departemenCount = response.data.data[0].departemenCount;
        const statusCount = response.data.data[0].statusCount;

        this.pieChart.data.labels = departemenCount.map(item => item.departemen);
        this.pieChart.data.series = departemenCount.map(item => item._count.departemen);

        console.log(response.data.data[0].statusCount)
        this.stats = [
          { category: 'Total Karyawan', count:  response.data.data[0].totalKaryawan },
          { category: 'Kontrak', count: (statusCount.find(status => status.status === 'kontrak') || { _count: { status: 0 } })._count.status },
          { category: 'Probation', count: (statusCount.find(status => status.status === 'probation') || { _count: { status: 0 } })._count.status },
          { category: 'Tetap', count: (statusCount.find(status => status.status === 'tetap') || { _count: { status: 0 } })._count.status }
        ];
      } catch (error) {
        console.error('Error fetching chart data:', error);
      }
    }
  },
  created() {
    this.fetchChartData();
  }
}

</script>
<style>

</style>
