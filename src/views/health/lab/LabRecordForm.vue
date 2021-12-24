<template>
  <ion-page>
    <ion-content>
      <ion-grid>
        <ion-row>
          <ion-col>
            <div class="ion-text-center">
              <ion-text>
                <h3>
                  บันทึกผลตรวจสุขภาพ
                </h3>
                <h4>Lab Tests Record</h4>
              </ion-text>
            </div>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-list>
              <ion-item>
                <ion-label position="floating">Date</ion-label>
                <ion-datetime display-format="MMM DD, YYYY HH:mm" v-model="labDateTime"></ion-datetime>
              </ion-item>
            </ion-list>
            <ion-list-header>
              <ion-text>
                <h5>
                  Physical
                </h5>
              </ion-text>
            </ion-list-header>
            <ion-list>
              <ion-item>
                <ion-col>
                  <ion-label>Systolic
                    <p>
                      ความดันโลหิต
                    </p>
                  </ion-label>
                </ion-col>
                <ion-col>
                  <ion-input placeholder="กรอกตัวเลข" v-model="systolic"></ion-input>
                </ion-col>
                <ion-col size="1">
                  <ion-label>/</ion-label>
                </ion-coL>
                <ion-col>
                  <ion-input placeholder="กรอกตัวเลข" v-model="diastolic"></ion-input>
                </ion-col>
              </ion-item>
              <ion-item>
                <ion-col>
                  <ion-label>Heart Rate
                    <p>
                      อัตราเต้นของหัวใจ
                    </p>
                  </ion-label>
                </ion-col>
                <ion-col>
                  <ion-input placeholder="กรอกตัวเลข" v-model="heartrate"></ion-input>
                </ion-col>
              </ion-item>
            </ion-list>

            <ion-list-header>
              <ion-text>
                <h5>
                  Lab
                </h5>
              </ion-text>
            </ion-list-header>
            <ion-list>
              <ion-item>
                <ion-col>
                  <ion-label>Glucose
                    <p>
                      น้ำตาลในเลือด
                    </p>
                  </ion-label>
                </ion-col>
                <ion-col>
                  <ion-input placeholder="กรอกตัวเลข" v-model="labGlu"></ion-input>
                </ion-col>
              </ion-item>
              <ion-item>
                <ion-col>
                  <ion-label>BUN
                    <p>
                      การทำงานของไต
                    </p>
                  </ion-label>
                </ion-col>
                <ion-col>
                  <ion-input placeholder="กรอกตัวเลข" v-model="labBUN"></ion-input>
                </ion-col>
              </ion-item>
              <ion-item>
                <ion-col>
                  <ion-label>Creatinine
                    <p>
                      การทำงานของไต
                    </p>
                  </ion-label>
                </ion-col>
                <ion-col>
                  <ion-input placeholder="กรอกตัวเลข" v-model="labCre"></ion-input>
                </ion-col>
              </ion-item>
              <ion-item>
                <ion-col>
                  <ion-label>Uric acid
                    <p>
                      ระดับกรดยูริกในเลือด
                    </p>
                  </ion-label>
                </ion-col>
                <ion-col>
                  <ion-input placeholder="กรอกตัวเลข" v-model="labUA"></ion-input>
                </ion-col>
              </ion-item>
              <ion-item>
                <ion-col>
                  <ion-label>Cholesterol
                    <p>
                      ตรวจกลุ่มไขมัน
                    </p>
                  </ion-label>
                </ion-col>
                <ion-col>
                  <ion-input placeholder="กรอกตัวเลข" v-model="labCHO"></ion-input>
                </ion-col>
              </ion-item>
              <ion-item>
                <ion-col>
                  <ion-label>Triglyceride
                    <p>
                      ตรวจกลุ่มไขมัน
                    </p>
                  </ion-label>
                </ion-col>
                <ion-col>
                  <ion-input placeholder="กรอกตัวเลข" v-model="labTG"></ion-input>
                </ion-col>
              </ion-item>
              <ion-item>
                <ion-col>
                  <ion-label>HDL-C
                    <p>
                      ตรวจกลุ่มไขมัน
                    </p>
                  </ion-label>
                </ion-col>
                <ion-col>
                  <ion-input placeholder="กรอกตัวเลข" v-model="labHDL"></ion-input>
                </ion-col>
              </ion-item>
              <ion-item>
                <ion-col>
                  <ion-label>LDL-C
                    <p>
                      ตรวจกลุ่มไขมัน
                    </p>
                  </ion-label>
                </ion-col>
                <ion-col>
                  <ion-input placeholder="กรอกตัวเลข" v-model="labLDL"></ion-input>
                </ion-col>
              </ion-item>
            </ion-list>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-button expand="block" :disabled="!isFormValid" color="success" @click="saveData">Save</ion-button>
          </ion-col>
        </ion-row>
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
  IonListHeader,
  IonList,
  IonLabel,
  IonInput,
  IonButton,
  IonDatetime,
  IonItem,
  IonIcon,
  IonFab,
  IonFabButton
} from '@ionic/vue';
import {defineComponent} from 'vue';
import {mapState} from "vuex";
import {db} from "@/firebase";
import { collection, addDoc, Timestamp } from '@firebase/firestore'
import { arrowBackCircle } from 'ionicons/icons'

export default defineComponent({
  name: "LabRecordForm",
  components: {
    IonText,
    IonGrid,
    IonRow,
    IonCol,
    IonContent,
    IonPage,
    IonListHeader,
    IonList,
    IonLabel,
    IonInput,
    IonButton,
    IonDatetime,
    IonItem,
    IonIcon,
    IonFab,
    IonFabButton
  },
  computed: {
    ...mapState(['user']),
    isFormValid() {
      return this.isNotBlank(this.labGlu) || this.isNotBlank(this.labBUN) || this.isNotBlank(this.labCre)
          || this.isNotBlank(this.labUA) || this.isNotBlank(this.labCHO)|| this.isNotBlank(this.labHDL)
          || this.isNotBlank(this.labTG) || this.isNotBlank(this.labLDL)
          || this.isNotBlank(this.heartrate)
          || (this.isNotBlank(this.systolic) && (this.isNotBlank(this.diastolic)))
    },
  },
  setup () {
    return {
      arrowBackCircle
    }
  },
  data() {
    return {
      labDateTime: new Date().toISOString(),
      labTest: {},
      labGlu: null,
      labBUN: null,
      labCre: null,
      labUA: null,
      labCHO: null,
      labTG: null,
      labHDL: null,
      labLDL: null,
      systolic: null,
      diastolic: null,
      heartrate: null,
    }
  },
  methods: {
    isNotBlank (value) {
      return value !== null && value !== ''
    },
    saveData() {
      const ref = collection(db, 'lab_records')
      let data = {
        userId: this.$store.state.user.userId,
        labDateTime: Timestamp.fromDate(new Date(this.labDateTime)),
        labTest:  {
          labGlu: this.labGlu,
          labBUN: this.labBUN,
          labCre: this.labCre,
          labUA: this.labUA,
          labCHO: this.labCHO,
          labTG: this.labTG,
          labHDL: this.labHDL,
          labLDL: this.labLDL,
          systolic: this.systolic,
          diastolic: this.diastolic,
          heartrate: this.heartrate,
        }
      }
      addDoc(ref, data).then((docRef)=>{
        data.id = docRef.id
        this.$router.push({ name: 'LabRecord' })
      })
    }
  }
});
</script>

<style scoped>

</style>
