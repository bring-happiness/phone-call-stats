<script>
import { Doughnut } from 'vue-chartjs';

export default {
  name: 'pie',
  extends: Doughnut,
  props: ['chartdata', 'options'],
  mounted() {
    this.renderChart(this.chartdata, this.options);
  },
};
</script>

<style scoped>

</style>
