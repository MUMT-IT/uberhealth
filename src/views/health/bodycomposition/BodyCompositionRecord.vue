<template>
  <ion-page>
    <ion-content>
      <ion-grid>
        <ion-row>
          <ion-col>
            <ion-text class="ion-text-lg-center">
              <h1>Body Composition Record</h1>
            </ion-text>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <p>FAT%</p>
            <canvas id="FATChart"></canvas>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <p>FAT MASS</p>
            <canvas id="FATMASSChart"></canvas>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <p>MUSCLE MASS</p>
            <canvas id="MuscleMASSChart"></canvas>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <p>METABOLIC AGE</p>
            <canvas id="metabolicChart"></canvas>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <p>VISCERAL FAT</p>
            <canvas id="visceralChart"></canvas>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-button expand="block" @click="$router.push({ name: 'BodyCompositionRecordForm' })">
              Add
            </ion-button>
          </ion-col>
        </ion-row>
      </ion-grid>
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
  IonText,
  IonButton
} from '@ionic/vue';
import {defineComponent} from 'vue';
import {mapState} from "vuex";
import {Chart, registerables} from 'chart.js';
import {db} from "@/firebase";
import {collection, getDocs, query, where} from '@firebase/firestore'
import {orderBy} from "firebase/firestore";

Chart.register(...registerables);
export default defineComponent({
  name: "BodyCompositionRecord",
  components: {
    IonText,
    IonGrid,
    IonRow,
    IonCol,
    IonContent,
    IonPage,
    IonButton
  },
  computed: {
    ...mapState(['user'])
  },
  data() {
    return {
      pot_fat: [],
      pot_fatmass: [],
      pot_musclemass: [],
      pot_BMR: [],
      pot_metabolic_age: [],
      pot_visceral_fat: [],
      pot_date: [],
    }
  },
  methods: {
    createChart(ctx, datasets, label, labels, bgColor) {
      this.bars = new Chart(ctx, {
        type: 'line',
        data: {
          labels: labels,
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
    },
    async BindChart() {
      let ref = collection(db, 'bodycomposition_records')
      let q = query(ref, where("userId", "==", this.$store.state.user.userId), orderBy('bodycomposidate', 'asc'))
      let querySnapshot = await getDocs(q)
      let databodycomp = null
      querySnapshot.forEach(d => {
        databodycomp = d.data()
        this.pot_fat.push(databodycomp.fat)
        this.pot_fatmass.push(databodycomp.fatmass)
        this.pot_musclemass.push(databodycomp.musclemass)
        this.pot_BMR.push(databodycomp.bmr)
        this.pot_metabolic_age.push(databodycomp.metabolic_age)
        this.pot_visceral_fat.push(databodycomp.visceralfat)

        let pdate = new Date(databodycomp.bodycomposidate.seconds * 1000)
        this.pot_date.push((pdate.getDate() + '/' + pdate.getMonth() + 1))
      })
      const fatctx = document.getElementById('FATChart').getContext('2d');
      let data_fat = this.pot_fat
      let labels_fat = this.pot_date
      let label_fat = '%'
      this.createChart(fatctx, data_fat, label_fat, labels_fat, 'rgb(38, 194, 129)')

      const fatmassctx = document.getElementById('FATMASSChart').getContext('2d');
      let data_fatmass = this.pot_fatmass
      let labels_fatmass = this.pot_date
      let label_fatmass = 'kg'
      this.createChart(fatmassctx, data_fatmass, label_fatmass, labels_fatmass, 'rgb(38, 194, 129)')

      const musclemassctx = document.getElementById('MuscleMASSChart').getContext('2d');
      let data_musclemass = this.pot_musclemass
      let labels_musclemass = this.pot_date
      let label_musclemass = 'kg'
      this.createChart(musclemassctx, data_musclemass, label_musclemass, labels_musclemass, 'rgb(38, 194, 129)')

      const metabolicctx = document.getElementById('metabolicChart').getContext('2d');
      let data_metabolic = this.pot_metabolic_age
      let labels_metabolic = this.pot_date
      let label_metabolic = ''
      this.createChart(metabolicctx, data_metabolic, label_metabolic, labels_metabolic, 'rgb(38, 194, 129)')

      const visceralctx = document.getElementById('visceralChart').getContext('2d');
      let data_visceral = this.pot_visceral_fat
      let labels_visceral = this.pot_date
      let label_visceral = ''
      this.createChart(visceralctx, data_visceral, label_visceral, labels_visceral, 'rgb(38, 194, 129)')
    }
  },
  updated() {
    this.BindChart()
  },
});
</script>

<style scoped>

</style>
