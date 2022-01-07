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
            <ion-list-header>
              <ion-text>
                <h3>
                  New record / รายการใหม่
                </h3>
              </ion-text>
            </ion-list-header>
            <ion-list>
              <ion-item>
                <ion-label position="floating">Start</ion-label>
                <ion-datetime display-format="MMM DD, YYYY HH:mm" v-model="bodycomposidate"></ion-datetime>
              </ion-item>
              <ion-item>
                <ion-label position="floating">FAT %</ion-label>
                <ion-input type="number" min="0.1" step="0.1" v-model="fat" placeholder="0.0"></ion-input>
              </ion-item>
              <ion-item>
                <ion-label position="floating">FAT MASS</ion-label>
                <ion-input type="number" min="0.1" step="0.1" v-model="fatmass" placeholder="0.0"></ion-input>
              </ion-item>
              <ion-item>
                <ion-label position="floating">MUSCLE MASS</ion-label>
                <ion-input type="number" min="0.1" step="0.1" v-model="musclemass" placeholder="0.0"></ion-input>
              </ion-item>
              <ion-item>
                <ion-label position="floating">BMR (kcal)</ion-label>
                <ion-input type="number" min="0" step="1000" v-model="bmr" placeholder="0"></ion-input>
              </ion-item>
              <ion-item>
                <ion-label position="floating">METABOLIC AGE</ion-label>
                <ion-input type="number" min="0" step="100" v-model="metabolic_age" placeholder="0"></ion-input>
              </ion-item>
              <ion-item>
                <ion-label position="floating">VISCERAL FAT</ion-label>
                <ion-input type="number" min="0" step="100" v-model="visceralfat" placeholder="0"></ion-input>
              </ion-item>
            </ion-list>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-button :disabled="!isFormValid" expand="block" color="success" @click="saveData">
              Save
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
  IonList,
  IonItem,
  IonDatetime,
  IonInput
} from '@ionic/vue';
import {defineComponent} from 'vue';
import {db} from "@/firebase";
import {collection, addDoc, Timestamp} from '@firebase/firestore'

export default defineComponent({
  name: "BodyCompositionRecordForm",
  components: {
    IonText,
    IonGrid,
    IonRow,
    IonCol,
    IonContent,
    IonPage,
    IonList,
    IonItem,
    IonDatetime,
    IonInput
  },
  data() {
    return {
      bodycomposidate: new Date().toISOString(),
      fat: '',
      fatmass: '',
      musclemass: '',
      metabolic_age: '',
      visceralfat: '',
      bmr: ''
    }
  },
  computed: {
    isFormValid() {
      return (this.startDateTime != '' || this.startDateTime != null)
    }
  },
  methods: {
    saveData() {
      if (this.isFormValid) {
        const ref = collection(db, 'bodycomposition_records')
        let data = {
          userId: this.$store.state.user.userId,
          bodycomposidate: Timestamp.fromDate(new Date(this.bodycomposidate)),
          fat: this.fat,
          fatmass: this.fatmass,
          musclemass: this.musclemass,
          metabolic_age: this.metabolic_age,
          visceralfat: this.visceralfat,
          bmr: this.bmr
        }
        addDoc(ref, data).then((docRef) => {
          data.id = docRef.id
        })
        this.$router.push({ name: 'BodyCompositionRecord' })
      }
    }
  }
});
</script>

<style scoped>

</style>
