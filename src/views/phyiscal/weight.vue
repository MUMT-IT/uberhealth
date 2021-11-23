<template>
  <ion-page>
    <ion-content>
      <ion-grid>
        <ion-row>
          <ion-col>
            <ion-text>
              <div class="ion-text-center">
                <h1> น้ำหนักตัว </h1>
              </div>
            </ion-text>
          </ion-col>
        </ion-row>
        <ion-row>
          <label>{{ new Date().toLocaleDateString() }}</label>
        </ion-row>
        <ion-row>
         <ion-list>
           <ion-item>
             <ion-label> weight </ion-label>
             <ion-input type="number" placeholder="70.5" v-model="weight"></ion-input>
             <ion-label> kg </ion-label>
           </ion-item>
         </ion-list>
        </ion-row>
          <ion-button expand="block" color="success" @click="savedata">Save</ion-button>
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
import { arrowBackCircle } from 'ionicons/icons';
import { Timestamp,collection,addDoc } from '@firebase/firestore';
import {
  IonContent,
  IonPage,
  IonGrid,
  IonRow,
  IonLabel,
  IonInput,
  IonFab,
  IonCol,
  IonText,
  IonButton
} from '@ionic/vue';
import {db} from "@/firebase";
export default {
  name: "weight",
  components :{
    IonContent,
    IonPage,
    IonGrid,
    IonRow,
    IonLabel,
    IonInput,
    IonFab,
    IonCol,
    IonText,
    IonButton
  },
  date(){
    return {
      DateNow: "",
      DateN: "",
      weight: ""
    }
  },
  setup () {
    return {
      arrowBackCircle,
    }
  },
  methods:{
    savedata(){
      const ref = collection(db, 'weight')
      let data ={
        userId: this.$store.state.user.userId,
        weightdate: Timestamp.fromDate(new Date()),
        datetxt: this.DateN,
        weight: this.weight
      }
      addDoc(ref, data).then((docRef)=> {
        data.id = docRef.id
      })}
  },
  mounted() {
    let strDateY = new Date().getFullYear()
    let strDateM = new Date().getMonth()
    let strDateD = new Date().getDate()
    this.DateN = strDateY.toString() + strDateM.toString() + strDateD.toString()
  }
}
</script>

<style scoped>

</style>