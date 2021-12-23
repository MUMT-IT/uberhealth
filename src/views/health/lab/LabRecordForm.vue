<template>
  <ion-page>
    <ion-content>
      <ion-grid>
        <ion-row>
          <ion-col>
            <ion-list-header>
              <ion-text>
                <h3>
                  Lab New record / รายการใหม่
                </h3>
              </ion-text>
            </ion-list-header>
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
                  <ion-input placeholder="กรอกตัวเลข" v-model="systolic1"></ion-input>
                </ion-col>
                <ion-col size="1">
                  <ion-label>/</ion-label>
                </ion-coL>
                <ion-col>
                  <ion-input placeholder="กรอกตัวเลข" v-model="systolic2"></ion-input>
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
  IonDatetime
} from '@ionic/vue';
import {defineComponent} from 'vue';
import {mapState} from "vuex";
import {db} from "@/firebase";
import { collection, addDoc, Timestamp } from '@firebase/firestore'

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
    IonDatetime
  },
  computed: {
    ...mapState(['user']),
    isFormValid() {
      return ((this.labGlu != null) && (this.labGlu != ''))
          || ((this.labBUN != null) && (this.labBUN != ''))
          || ((this.labCre != null) && (this.labCre != ''))
          || ((this.labUA != null) && (this.labUA != ''))
          || ((this.labCHO != null) && (this.labCHO != ''))
          || ((this.labHDL != null) && (this.labHDL != ''))
          || ((this.labTG != null) && (this.labTG != ''))
          || ((this.labLDL != null) && (this.labLDL != ''))
          || ((this.systolic1 != null) && (this.systolic1 != '') && (this.systolic2 != null) && (this.systolic2 != ''))
          || ((this.heartrate != null) && (this.heartrate != ''))
    },
  },
  data() {
    return {
      labDateTime: new Date().toISOString(),
      labTest: [],
      labGlu: null,
      labBUN: null,
      labCre: null,
      labUA: null,
      labCHO: null,
      labTG: null,
      labHDL: null,
      labLDL: null,
      systolic1: null,
      systolic2: null,
      heartrate: null,
    }
  },
  methods: {
    addLabTest(){
      if((this.labGlu != null) && (this.labGlu != '')){
        let lab = {'labGlu' : this.labGlu}
        this.labTest.push(lab)
      }
      if((this.labBUN != null) && (this.labBUN != '')){
        let lab = {'labBUN' : this.labBUN}
        this.labTest.push(lab)
      }
      if((this.labCre != null) && (this.labCre != '')){
        let lab = {'labCre' : this.labCre}
        this.labTest.push(lab)
      }
      if((this.labUA != null) && (this.labUA != '')){
        let lab = {'labUA' : this.labUA}
        this.labTest.push(lab)
      }
      if((this.labCHO != null) && (this.labCHO != '')){
        let lab = {'labCHO' : this.labCHO}
        this.labTest.push(lab)
      }
      if((this.labTG != null) && (this.labTG != '')){
        let lab = {'labTG' : this.labTG}
        this.labTest.push(lab)
      }
      if((this.labHDL != null) && (this.labHDL != '')){
        let lab = {'labHDL' : this.labHDL}
        this.labTest.push(lab)
      }
      if((this.labLDL != null) && (this.labLDL != '')){
        let lab = {'labLDL' : this.labLDL}
        this.labTest.push(lab)
      }
      if((this.systolic1 != null) && (this.systolic1 != '') && (this.systolic2 != null) && (this.systolic2 != '')){
        let lab = {
          'systolic1' : this.systolic1,
          'systolic2' : this.systolic2
        }
        this.labTest.push(lab)
      }
      if((this.heartrate != null) && (this.heartrate != '')){
        let lab = {'heartrate' : this.heartrate}
        this.labTest.push(lab)
      }
    },
    saveData() {
      this.addLabTest()
      const ref = collection(db, 'lab_records')
      let data = {
        userId: this.$store.state.user.userId,
        labDateTime: Timestamp.fromDate(new Date(this.labDateTime)),
        labTest:  this.labTest
      }
      addDoc(ref, data).then((docRef)=>{
        data.id = docRef.id
        this.$router.push({ name: 'BikeRecord' })
      })
    }
  }
});
</script>

<style scoped>

</style>
