<template>
  <div id="app">

    <button
      @click="showForSharing = !showForSharing"
      style="margin-right: 17px;"
    >
      <span v-if="showForSharing">
        Mode édition
      </span>

      <span v-else>
        Mode affichage
      </span>
    </button>

    <button @click="copyPageToClipboard">COPIER</button>

    <div
      v-if="isCopied"
      style="color: forestgreen"
    >
      Image copiée !
    </div>

    <h1>
      Nombre total d'appels aujourd'hui :

      <div style="font-size: 3.5rem; text-decoration: underline">
        {{ total }}
      </div>
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
            @click="onDecrement(category)">-
          </button>

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
      @click="resetStats"
      style="background: red; color: white;"
    >
      RÉINITIALISER LES STATS
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
import { toPng } from 'html-to-image';
import Pie from './pie.vue';

export default {

  components: {
    Pie,
  },

  created() {
    const stats = window.localStorage.getItem('stats');

    if (stats !== null) {
      this.categories = JSON.parse(stats);
    }
  },

  data() {
    return {
      showChart: false,
      showForSharing: false,
      isCopied: false,
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
          title: 'Il faut envoyer un email',
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
        labels: ['Intéressé', 'Répondeur', 'À rappeler', 'Il faut envoyer un email', 'Pas intéressé'],
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
        width: '400px',
        position: 'relative',
      };
    },
  },

  methods: {
    async copyPageToClipboard() {
      this.showForSharing = true;
      await this.toggleChart();

      setTimeout(() => {
        const node = document.getElementById('app');

        toPng(node)
          .then((dataUrl) => {
            const img = new Image();
            img.src = dataUrl;
            img.id = 'screenshot';
            document.body.appendChild(img);

            const imageElem = document.querySelector('#screenshot');
            const range = document.createRange();
            range.selectNode(imageElem);
            window.getSelection()
              .addRange(range);

            try {
              // Now that we've selected the anchor text, execute the copy command
              const successful = document.execCommand('copy');
              const msg = successful ? 'successful' : 'unsuccessful';
              console.log(`Copy image command was ${msg}`);
            } catch (err) {
              console.log('Oops, unable to copy');
            }

            window.getSelection()
              .removeAllRanges();

            document.body.removeChild(img);

            this.isCopied = true;

            setTimeout(() => {
              this.isCopied = false;
            }, 2000);
          })
          .catch((error) => {
            console.error('oops, something went wrong!', error);
          });
      }, 1000);
    },

    onIncrement(category) {
      // eslint-disable-next-line no-param-reassign
      category.number += 1;
      this.saveInStorage();
    },

    onDecrement(category) {
      // eslint-disable-next-line no-param-reassign
      category.number -= 1;
      this.saveInStorage();
    },

    saveInStorage() {
      window.localStorage.setItem('stats', JSON.stringify(this.categories));
    },

    async toggleChart() {
      this.showChart = false;

      await this.$nextTick();

      this.showChart = true;
    },

    resetStats() {
      this.categories.forEach((category) => {
        // eslint-disable-next-line no-param-reassign
        category.number = 0;
      });
    },
  },
};
</script>

<style lang="scss">

.cell {
  border: 1px solid black;
  width: 20%;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

</style>
