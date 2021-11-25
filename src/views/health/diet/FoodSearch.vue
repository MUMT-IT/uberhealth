<template>
  <ion-page>
    <ion-content>
      <ion-grid>
        <ion-row>
          <ion-col>
            <ion-text>
              <div class="ion-text-center">
                <h1>Food</h1>
                <p>{{ $store.state.user.displayName }}</p>
              </div>
            </ion-text>
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
              <label>ชื่ออาหาร {{ FoodRec.FoodName }}
                <p>
                  Carbo {{ FoodRec.Carbo }} %
                </p>
                <p>
                  Protein {{ FoodRec.Protein }} %
                </p>
                <p>
                  Vegetable {{ FoodRec.Vegetable }} %
                </p>
              </label>
            </ion-item>
          </ion-list>
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
  IonSearchbar,
  IonList,
  IonItem
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
    IonItem
  },
  data(){
    return{
      FoodData: []
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
      querySnapshot.forEach(d => {
        let data = d.data()
        data.id = d.id
        this.FoodData = []
        this.FoodData.push(data)
      })
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