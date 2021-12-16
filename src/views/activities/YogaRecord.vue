<template>
  <ion-page>
    <ion-content>
      <ion-grid>
        <ion-row>
          <ion-col>
            <ion-text>
              <div class="ion-text-center">
                <h1>Yoga Record</h1>
                <p>{{ this.user.displayName }}</p>
              </div>
            </ion-text>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-list>
              <ion-item detail v-for="record in yogasort.slice(0,7)"
                        :key="record.id" @click="goToDetail(record.id)">
                <ion-label>
                  {{ record.startDateTime.toDate().toLocaleString() }}
                  <p>
                    {{ ArraytoText(record.yogaposes) }}
                    {{ record.min }} min,
                    {{ record.estimatedCalories.toFixed(1) }} Cal
                  </p>
                </ion-label>
              </ion-item>
            </ion-list>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-button expand="block" @click="$router.push({ name: 'YogaRecordForm' })">
              Add
            </ion-button>
          </ion-col>
        </ion-row>
      </ion-grid>
      <ion-fab vertical="top" horizontal="start" slot="fixed">
        <ion-fab-button @click="$router.push({ name: 'Exercise' })">
          <ion-icon :icon="arrowBackCircle"></ion-icon>
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
  IonText,
  IonButton,
  IonList,
  IonItem,
  IonFabButton,
  IonFab,
  IonLabel,
  IonIcon,
} from '@ionic/vue';

import {defineComponent} from 'vue';
import { arrowBackCircle } from 'ionicons/icons'
import {mapGetters, mapState} from "vuex";

export default defineComponent({
  name: "YogaRecord",
  components: {
    IonContent,
    IonPage,
    IonGrid,
    IonRow,
    IonCol,
    IonText,
    IonButton,
    IonList,
    IonItem,
    IonLabel,
    IonFabButton,
    IonFab,
    IonIcon,
  },
  data() {
    return {
      yogasort : []
    }
  },
  setup () {
    return {
      arrowBackCircle,
    }
  },
  computed: {
    ...mapState(['user', 'activity_records']),
    ...mapGetters(['YogaRecords']),
  },
  methods: {
    goToDetail (recordId) {
      this.$router.push({ name: 'YogaRecordDetail', params: { recordId: recordId}})
    },
    ArraytoText(arr){
      let txt =  ''
      for (const arrKey in arr) {
        txt += arr[arrKey] + ','
      }
      return txt
    }
  },
  updated() {
    this.yogasort = this.YogaRecords
    this.yogasort.sort((a,b)=> b.startDateTime-a.startDateTime)
  }
})
</script>

<style scoped>

</style>