<template>
  <ion-page>
    <ion-content>
      <ion-grid>
        <ion-row>
          <ion-col>
            <ion-text>
              <div class="ion-text-center">
                <h1>Food</h1>
                <p>ค้นหารายการอาหาร</p>
              </div>
            </ion-text>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-item>
              <ion-label position="floating">Date</ion-label>
              <ion-datetime display-format="MMM DD, YYYY HH:mm" v-model="FoodDateTime"></ion-datetime>
            </ion-item>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-searchbar placeholder="enter key" inputmode="search" animated debounce="100" v-model="keywords" @ionChange="change">
            </ion-searchbar>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-list>
            <ion-item v-for="FoodRec in FoodData" :key="FoodRec.id">
              <label>ชื่ออาหาร {{ keywords }}
              </label>
            </ion-item>
          </ion-list>
        </ion-row>
      </ion-grid>
      <ion-grid>
        <ion-row>
          <ion-label>Carbon: {{ FoodCarbo }} %</ion-label>
        </ion-row>
        <ion-row>
          <ion-range  min="0" max="100" v-model="FoodCarbo" step="5" ticks="true" snaps="true" color="warning"></ion-range>
        </ion-row>
        <ion-row>
          <ion-label>Protein: {{ FoodProtein }} %</ion-label>
        </ion-row>
        <ion-row>
          <ion-range  min="0" max="100" v-model="FoodProtein" step="5" ticks="true" snaps="true" color="secondary"></ion-range>
        </ion-row>
        <ion-row>
          <ion-label>Vegetable: {{ FoodVegetable }} %</ion-label>
        </ion-row>
        <ion-row>
          <ion-range  min="0" max="100" v-model="FoodVegetable" step="5" ticks="true" snaps="true" color="success"></ion-range>
        </ion-row>
        <ion-row>
          <ion-item>
            <ion-label slot="start">Calorie</ion-label>
            <ion-input placeholder="100"></ion-input>
            <ion-label slot="end">Kcal</ion-label>
          </ion-item>
        </ion-row>
        <ion-row>
          <ion-item>
            <ion-label slot="start">Unit</ion-label>
            <ion-input placeholder="300"></ion-input>
            <ion-label slot="end">g</ion-label>
          </ion-item>
        </ion-row>
      </ion-grid>
      <ion-button expand="block" color="success" >เพิ่มรายการ</ion-button>
      <p>
        <label>สารอาหารทั้งวัน</label>
      </p>
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
  IonSearchbar,
  IonList,
  IonItem,
  IonButton,
  IonRange,
  IonDatetime
} from '@ionic/vue';
import {db} from "@/firebase";
import { collection,getDocs, query, where } from '@firebase/firestore'
import {ref} from 'vue';
export default {
  name: "FoodSearch",
  components: {
    IonContent,
    IonPage,
    IonGrid,
    IonRow,
    IonCol,
    IonText,
    IonSearchbar,
    IonList,
    IonItem,
    IonButton,
    IonRange,
    IonDatetime
  },
  data(){
    return{
      FoodData: [],
      FoodCarbo: 0,
      FoodProtein: 0,
      FoodVegetable: 0,
      FoodDateTime: new Date().toISOString(),
    }
  },
  watch:{
    keywords: async function () {
      await this.LoadFoodData()
    }
  },
  methods:{
    async LoadFoodData(){
      let ref = collection(db, 'Food')
      let q = query(ref, where("FoodName", "==", this.keywords))
      let querySnapshot = await getDocs(q)
      if (querySnapshot.empty){
        this.FoodData = [{ FoodName: this.keywords }]
        this.FoodCarbo = 0
        this.FoodProtein = 0
        this.FoodVegetable = 0
      }else{
        querySnapshot.forEach(d => {
          let data = d.data()
          data.id = d.id
          this.FoodData = []
          this.FoodData.push(data)
          this.FoodCarbo = data.Carbo
          this.FoodProtein = data.Protein
          this.FoodVegetable = data.Vegetable
        })
      }

    }
  },
  setup(){
    const keywords = ref("");
    const change  = () => {
      // The value returned is the last updated value，Not the current value
      console.log(keywords.value);
    }
    return {
      keywords,
      change,
    };
  }
}
</script>

<style scoped>

</style>