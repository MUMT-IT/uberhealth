<template>
  <ion-page>
    <ion-content>
      <ion-grid>
        <ion-row>
          <ion-col>
            <ion-text>
              <div class="ion-text-center">
                <h1>Drink Record</h1>
                <p>{{ this.user.displayName }}</p>
              </div>
            </ion-text>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-list>
              <ion-item detail v-for="record in drinkRecords"
                        :key="record.id" @click="goToDetail(record.id)">
                <ion-label>
                  {{ record.startDateTime.toDate().toLocaleString() }}
                </ion-label>
              </ion-item>
            </ion-list>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <div class="ion-text-center">
              <ion-button size="large" fill="clear" @click="water50">+</ion-button>
            </div>
          </ion-col>
          <ion-col>
            <div class="ion-text-center">
              <h2><h2><ion-label>{{ drinkwater }}</ion-label></h2></h2>
            </div>
          </ion-col>
          <ion-col>
            <div class="ion-text-center">
              <ion-button size="large" fill="clear" @click="water_50">-</ion-button>
            </div>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <h5><ion-label>ทั้งวันควรดื่ม</ion-label></h5>
          </ion-col>
          <ion-col>
            <div class="ion-text-center">
              <h2><ion-label>1815</ion-label></h2>
            </div>
          </ion-col>
          <ion-col>
            <h2><ion-label>ml</ion-label></h2>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-button expand="block" @click="water1500">
              1500 ml
            </ion-button>
          </ion-col>
          <ion-col>
            <ion-button expand="block" @click="water600">
              600 ml
            </ion-button>
          </ion-col>
          <ion-col>
            <ion-button expand="block" @click="water100">
              350 ml
            </ion-button>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-button expand="block" color="success" @click="savedata">SAVE</ion-button>
          </ion-col>
        </ion-row>
<!--        <ion-row>
          <ion-col>
            <ion-button expand="block" @click="$router.push({ name: 'WalkRecordForm' })">
              Add
            </ion-button>
          </ion-col>
        </ion-row>-->
      </ion-grid>
      <ion-fab vertical="top" horizontal="start" slot="fixed">
        <ion-fab-button @click="$router.push({ name: 'Health' })">
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
  //IonInput
} from '@ionic/vue';

import {defineComponent} from 'vue';
import { arrowBackCircle } from 'ionicons/icons'
import {mapState} from "vuex";
import { db } from '@/firebase'
import { collection, addDoc,Timestamp } from '@firebase/firestore'

export default defineComponent({
  name: "Drink",
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
    //IonInput
  },
  setup () {
    return {
      arrowBackCircle,
    }
  },
  data(){
    return{
      drinkwater: 0
    }
  },
  computed: {
    ...mapState(['user',"drink_records"]),
  },
  methods: {
    goToDetail () {
      this.$router.push({ name: 'Health'})
    },
    water1500(){
      this.drinkwater += 1500
    },
    water600(){
      this.drinkwater += 600
    },
    water100(){
      this.drinkwater += 350
    },
    water50(){
      this.drinkwater += 50
    },
    water_50(){
      this.drinkwater -= 50
    },
    savedata(){
      const ref = collection(db, 'drink_records')
      let data ={
        userId: this.$store.state.user.userId,
        drinkwater: this.drinkwater,
        drnkdate: Timestamp.fromDate(new Date())
      }
      addDoc(ref, data).then((docRef)=> {
        data.id = docRef.id
        this.$store.dispatch('addDrinkRecords', data)
      })
    }
  }
})
</script>

<style scoped>

</style>