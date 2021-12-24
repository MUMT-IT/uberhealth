<template>
  <ion-page>
    <ion-content>
      <ion-grid>
        <ion-row>
          <ion-col>
            <div class="ion-text-center">
              <ion-text>
                <h1>Lab Tests Record</h1>
              </ion-text>
            </div>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-card>
              <ion-card-header>
                <ion-card-title>
                  Glucose
                  <ion-chip color="success">
                    <ion-label>Normal</ion-label>
                  </ion-chip>
                </ion-card-title>
              </ion-card-header>
              <ion-card-content>
                <canvas id="GlucChart"></canvas>
              </ion-card-content>
            </ion-card>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-card>
              <ion-card-header>
                <ion-card-title>
                  Cholesterol
                  <ion-chip color="warning">
                    <ion-label>Warning</ion-label>
                  </ion-chip>
                </ion-card-title>
              </ion-card-header>
              <ion-card-content>
                <canvas id="CHOChart"></canvas>
              </ion-card-content>
            </ion-card>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-card>
              <ion-card-header>
                <ion-card-title>
                  Triglyceride
                  <ion-chip color="success">
                    <ion-label>Normal</ion-label>
                  </ion-chip>
                </ion-card-title>
              </ion-card-header>
              <ion-card-content>
                <canvas id="TGChart"></canvas>
              </ion-card-content>
            </ion-card>
          </ion-col>
        </ion-row>
      </ion-grid>
      <ion-fab vertical="top" horizontal="start" slot="fixed">
        <ion-fab-button @click="$router.push({ name: 'Health' })">
          <ion-icon :icon="arrowBackCircle"></ion-icon>
        </ion-fab-button>
      </ion-fab>
      <ion-fab vertical="bottom" horizontal="end" slot="fixed">
        <ion-fab-button color="success" @click="$router.push({ name: 'LabRecordForm' })">
          <ion-icon :icon="addOutline"></ion-icon>
        </ion-fab-button>
      </ion-fab>
    </ion-content>
  </ion-page>
</template>

<script>
import {
  IonContent,
  IonPage,
  IonGrid,
  IonRow,
  IonCol,
  IonCard,
  IonCardHeader,
  IonCardContent,
  IonText,
  IonIcon,
  IonFab,
  IonFabButton,
  IonLabel,
  IonChip,
  IonCardTitle,
} from '@ionic/vue';
import {defineComponent} from 'vue';
import { Chart, registerables } from 'chart.js';
Chart.register(...registerables);
import {mapState} from "vuex";
import { arrowBackCircle, addOutline } from 'ionicons/icons'

export default defineComponent({
  name: "LabRecord",
  components: {
    IonText,
    IonGrid,
    IonRow,
    IonCol,
    IonContent,
    IonPage,
    IonCard,
    IonCardHeader,
    IonCardContent,
    IonIcon,
    IonFab,
    IonFabButton,
    IonLabel,
    IonChip,
    IonCardTitle,
  },
  setup () {
    return {
      arrowBackCircle,
      addOutline
    }
  },
  computed: {
    ...mapState(['user'])
  },
  methods: {
    createChart(ctx, datasets, label, bgColor) {
      this.bars = new Chart(ctx, {
        type: 'line',
        data: {
          labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'July', 'Aug'],
          datasets: [{
            label: label,
            data: datasets,
            backgroundColor: bgColor, // array should have same number of elements as number of dataset
            borderColor: bgColor,// array should have same number of elements as number of dataset
            borderWidth: 2
          }]
        },
        options: {
          responsive: true,
          lineTension: .3,
          pointRadius: 4,
          plugins: {
            legend: {
              display: false
            }
          }
        }
      });
    }
  },
  mounted() {
    const glucctx = document.getElementById('GlucChart').getContext('2d');
    let data = [112, 98, 100, 97, 87, 101, 105, 111]
    let label = 'mg/dL'
    this.createChart(glucctx, data, label, 'rgb(38, 194, 129)')
    const choctx = document.getElementById('CHOChart').getContext('2d');
    data = [250, 205, 201, 195, 187, 220, 198, 221]
    label = 'mg/dL'
    this.createChart(choctx, data, label, 'rgb(250, 185, 35)')
    const tgctx = document.getElementById('TGChart').getContext('2d');
    data = [250, 205, 201, 195, 187, 220, 198, 221]
    label = 'mg/dL'
    this.createChart(tgctx, data, label, 'rgb(149, 57, 191)')
  },
});
</script>

<style scoped>

</style>
