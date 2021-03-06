<template>
  <div id="app">

    <button
      @click="showForSharing = !showForSharing"
    >
      <span v-if="showForSharing">
        Mode édition
      </span>

      <span v-else>
        Mode affichage
      </span>
    </button>

    <h1>
      Nombre total d'appels aujourd'hui :
      {{ total }}
    </h1>

    <table style="width: 100%; border: 1px solid black; border-collapse: collapse;">
      <tr>
        <th
          v-for="category in categories"
          :key="category.title"
          class="cell"
          :style="`background: ${category.color};`"
          style="color: white;"
        >
          {{ category.title }}
        </th>
      </tr>

      <tr>
        <td
          v-for="category in categories"
          :key="category.title"
          class="cell"
        >
          <button
            v-if="!showForSharing"
            @click="onDecrement(category)">-</button>

          <span style="font-size: 2.5rem; margin: 0 7px;">{{ category.number }}</span>

          <button
            v-if="!showForSharing"
            @click="onIncrement(category)"
            style=" font-size: 2.5rem; color: white; background: cadetblue; cursor: pointer;"
          >
            +
          </button>
        </td>
      </tr>
    </table>

    <button
      v-if="!showForSharing"
      @click="toggleChart"
    >
      AFFICHER LE GRAPHIQUE
    </button>

    <div class="margin: auto; width: 100%;">
      <pie
        v-if="showChart"
        :chartdata="chartData"
        :options="chartOptions"
        :styles="myStyles"
        style="margin: auto;"
      ></pie>
    </div>

  </div>

</template>

<script>
import Pie from './pie.vue';

export default {

  components: {
    Pie,
  },

  data() {
    return {
      showChart: false,
      showForSharing: false,
      categories: [
        {
          title: 'Intéressé',
          number: 0,
          color: '#03fcb1',
        },
        {
          title: 'Répondeur',
          number: 0,
          color: '#4d0000',
        },
        {
          title: 'À rappeler',
          number: 0,
          color: '#00D8FF',
        },
        {
          title: 'Il faut envoyé un email',
          number: 0,
          color: '#bb6ffd',
        },
        {
          title: 'Pas intéressé',
          number: 0,
          color: '#E46651',
        },
      ],

      chartOptions: {
        hoverBorderWidth: 20,
      },

    };
  },

  computed: {
    total() {
      let result = 0;

      this.categories.forEach((category) => {
        result += category.number;
      });

      return result;
    },

    chartData() {
      return {
        hoverBackgroundColor: 'red',
        hoverBorderWidth: 10,
        labels: ['Intéressé', 'Répondeur', 'À rappeler', 'Pas intéressé', 'Il faut envoyé un email'],
        datasets: [
          {
            label: 'Data One',
            backgroundColor: ['#03fcb1', '#4d0000', '#00D8FF', '#bb6ffd', '#E46651'],
            data: [
              this.categories[0].number,
              this.categories[1].number,
              this.categories[2].number,
              this.categories[3].number,
              this.categories[4].number,
            ],
          },
        ],
      };
    },

    myStyles() {
      return {
        width: '500px',
        position: 'relative',
      };
    },
  },

  methods: {
    onIncrement(category) {
      // eslint-disable-next-line no-param-reassign
      category.number += 1;
    },

    onDecrement(category) {
      // eslint-disable-next-line no-param-reassign
      category.number -= 1;
    },

    async toggleChart() {
      this.showChart = false;

      await this.$nextTick();

      this.showChart = true;
    },
  },
};
</script>

<style lang="scss">

.cell {
  border: 1px solid black; width: 20%;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

</style>
