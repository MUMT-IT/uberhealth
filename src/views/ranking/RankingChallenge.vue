<template>
  <ion-page>
    <ion-content>
      <ion-grid>
        <ion-row>
          <ion-col>
            <ion-text>
              <div class="ion-text-center">
                <h1>Ranking</h1>
                <p>{{ challengeTitle }}</p>
              </div>
            </ion-text>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-button color="success" @click="ReLoad">Load Ranking</ion-button>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-list>
              <ion-list-header>
                <h4>You</h4>
              </ion-list-header>
              <ion-item v-for="(ustep,index) in userStepsDisplay.slice(CaculateUpindex,pointIndex+2)" :key="ustep.id">
                <ion-col>
                  <ion-label>
                    {{ pointIndex + index }}
                  </ion-label>
                </ion-col>
                <ion-col size="7">
                  <ion-label>{{ ustep.username }}
                    <p>
                      {{ ustep.groupname }}
                    </p>
                  </ion-label>
                </ion-col>
                <ion-col>
                  <ion-badge color="warning">{{ ustep.steps }}</ion-badge>
                </ion-col>
                <ion-col>
                  <ion-label class="ion-text-right">steps</ion-label>
                </ion-col>
              </ion-item>
            </ion-list>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-list>
            <ion-list-header>
              <h4>Rank Groups</h4>
            </ion-list-header>
            <ion-item v-for="(gstep,index) in groupStepsDisplay" :key="gstep.id">
              <ion-col>
                <ion-label>
                  {{ index+1 }}
                </ion-label>
              </ion-col>
              <ion-col size="7">
                <ion-label>{{ gstep.groupname }}
                <p>
                  {{ gstep.countPerson }} คน
                </p>
                </ion-label>
              </ion-col>
              <ion-col>
                <ion-badge color="success">{{ gstep.steps }}</ion-badge>
              </ion-col>
              <ion-col>
                <ion-label class="ion-text-right">steps</ion-label>
              </ion-col>
            </ion-item>
            </ion-list>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-list>
              <ion-list-header>
                <h4>Rank Top 10</h4>
              </ion-list-header>
              <ion-item v-for="(ustep,index) in userStepsDisplay.slice(0,10)" :key="ustep.id">
                <ion-col>
                  <ion-label>
                    {{ index+1 }}
                  </ion-label>
                </ion-col>
                <ion-col size="7">
                  <ion-label>{{ ustep.username }}
                  <p>
                    {{ ustep.groupname }}
                  </p>
                  </ion-label>
                </ion-col>
                <ion-col>
                  <ion-badge color="danger">{{ ustep.steps }}</ion-badge>
                </ion-col>
                <ion-col>
                  <ion-label class="ion-text-right">steps</ion-label>
                </ion-col>
              </ion-item>
            </ion-list>
          </ion-col>
        </ion-row>
        <ion-row>
        </ion-row>
      </ion-grid>
      <ion-fab vertical="top" horizontal="start" slot="fixed">
        <ion-fab-button @click="$router.push({ name: 'Ranking' })">
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
  IonList,
  IonLabel,
  IonListHeader,
  IonFabButton,
  IonFab,
  IonIcon,
  IonButton,
  IonBadge
} from '@ionic/vue';
import {defineComponent} from 'vue';
import { arrowBackCircle } from 'ionicons/icons'
import {mapState} from "vuex";
import { db } from '@/firebase';
//import { doc } from '@firebase/firestore';
import {getDoc,doc,collection,where,getDocs,query} from "firebase/firestore";
export default defineComponent({
  props: {
    timeout: { type: Number, default: 7000 },
  },
  name: "RankingChallenge",
  components: {
    IonText,
    IonGrid,
    IonRow,
    IonCol,
    IonContent,
    IonPage,
    IonList,
    IonLabel,
    IonListHeader,
    IonFabButton,
    IonFab,
    IonIcon,
    IonButton,
    IonBadge
  },
  setup () {
    return {
      arrowBackCircle,
    }
  },
  data(){
    return{
      upIndex: 0,
      pointIndex: 0,
      downIndex:0,
      usteps:[],
      userStepsDisplay:[],
      gsteps:[],
      groupStepsDisplay:[],
      challengeId: '',
      userGroupsId: null,
      userMembers: {},
      userStep: {},
      groupNameStep: {},
      countPerson: {},
      challengeTitle: '',
    }
  },
  computed: {
    ...mapState(['user','profile','challenges','userGroup']),
    CaculateUpindex(){
      let upindex = 0
      if(this.pointIndex != 0){
        upindex = this.pointIndex
        upindex = upindex  -1
      }
      return upindex
    },
  },
  watch:{
    challengeId: async function() {
      await this.ResetData()
      await this.Loadactivity_submission()
    }
  },
  methods:{
    ReLoad(){
      this.ResetData()
      this.Loadactivity_submission()
    },
    ResetData(){
      this.userStep = {}
      this.groupNameStep = {}
      this.countPerson = {}
      this.usteps = []
      this.gsteps =[]
    },
    async Loadactivity_submission(){
        let ref = collection(db, 'activity_submission')
        let q = query(ref, where('challengeId', '==',this.challengeId ))
        let querySnapshot = await getDocs(q)
        querySnapshot.forEach(d => {
          let  data = d.data()
          data.id = d.id
          'data.steps ต้องเปลี่ยนให้อ่านจาก challenge'
          if(data.steps > 0 ){
            'GroupName Step'
            if(this.groupNameStep[data.userGroupId] === undefined){
              this.groupNameStep[data.userGroupId] = parseInt(data.steps)
              this.gsteps.push({
                groupid: data.userGroupId,
                steps: data.steps,
                groupname: null,
                countPerson: 0,
              })
            }else{
              this.groupNameStep[data.userGroupId] += parseInt(data.steps)
              this.gsteps.find(gsteps => gsteps.groupid === data.userGroupId).steps = parseInt(this.groupNameStep[data.userGroupId])
            }

            'user Step'
            if(this.userStep[data.userId] === undefined){
              if(this.countPerson[data.userGroupId] === undefined){
                this.countPerson[data.userGroupId] = 1
              }else{
                this.countPerson[data.userGroupId] += 1
              }
              this.gsteps.find(gsteps => gsteps.groupid === data.userGroupId).countPerson = parseInt(this.countPerson[data.userGroupId])
              this.userStep[data.userId] = parseInt(data.steps)
              this.usteps.push({
                uid: data.userId,
                steps: this.userStep[data.userId],
                groupid: data.userGroupId,
                groupname: '',
                username: ''
              })
            }else{
              this.userStep[data.userId] += parseInt(data.steps)
              this.usteps.find(usteps => usteps.uid === data.userId).steps = parseInt(this.userStep[data.userId])
            }
          }
        })
      this.usteps.sort((a,b)=> b.steps-a.steps)
      this.gsteps.sort((a,b)=> b.steps-a.steps)
      if(this.usteps.length != 0 ){
        'Show 10 Rank UserName'
        for (let i = 0; i < 9; i++) {
          if(i <= this.usteps.length -1){
            let ref = collection(db, 'users')
            let q = query(ref, where("userId", "==", this.usteps[i].uid))
            let querySnapshot = await getDocs(q)
            if (querySnapshot.docs.length!==0){
              let docSnapshot = querySnapshot.docs[0]
              let data = docSnapshot.data()
              this.usteps.find(usteps => usteps.uid === this.usteps[i].uid).username =  data.displayName
            }
            let docRef =  doc(db, 'userGroups', this.usteps[i].groupid)
            await getDoc(docRef).then((snapshot) => {
              if (snapshot.exists()) {
                let data = snapshot.data()
                data.id = snapshot.id
                this.usteps.find(usteps => usteps.uid === this.usteps[i].uid).groupname =  data.name
              }
            })
          }
        }
        'Show UserName'
        this.pointIndex = this.usteps.findIndex(usteps => usteps.uid === this.$store.state.user.userId)
        if(this.pointIndex == 0 ){
          if(this.usteps[this.pointIndex].username == ''){
            let ref = collection(db, 'users')
            let q = query(ref, where("userId", "==", this.usteps[this.pointIndex].uid))
            let querySnapshot = await getDocs(q)
            if (querySnapshot.docs.length!==0){
              let docSnapshot = querySnapshot.docs[0]
              let data = docSnapshot.data()
              this.usteps.find(usteps => usteps.uid === this.usteps[this.pointIndex].uid).username =  data.displayName
            }
            let docRef =  doc(db, 'userGroups', this.usteps[this.pointIndex].groupid)
            await getDoc(docRef).then((snapshot) => {
              if (snapshot.exists()) {
                let data = snapshot.data()
                data.id = snapshot.id
                this.usteps.find(usteps => usteps.uid === this.usteps[this.pointIndex].uid).groupname =  data.name
              }
            })
          }
          if(this.usteps.length > 1){
            if(this.usteps[this.pointIndex +1 ].username == ''){
              let ref = collection(db, 'users')
              let q = query(ref, where("userId", "==", this.usteps[this.pointIndex +1].uid))
              let querySnapshot = await getDocs(q)
              if (querySnapshot.docs.length!==0){
                let docSnapshot = querySnapshot.docs[0]
                let data = docSnapshot.data()
                this.usteps.find(usteps => usteps.uid === this.usteps[this.pointIndex + 1 ].uid).username =  data.displayName
              }
              let docRef =  doc(db, 'userGroups', this.usteps[this.pointIndex +1].groupid)
              await getDoc(docRef).then((snapshot) => {
                if (snapshot.exists()) {
                  let data = snapshot.data()
                  data.id = snapshot.id
                  this.usteps.find(usteps => usteps.uid === this.usteps[this.pointIndex +1].uid).groupname =  data.name
                }
              })
            }
          }
          this.pointIndex = 1
        }else{
          if(this.usteps[this.pointIndex].username == ''){
            let ref = collection(db, 'users')
            let q = query(ref, where("userId", "==", this.usteps[this.pointIndex].uid))
            let querySnapshot = await getDocs(q)
            if (querySnapshot.docs.length!==0){
              let docSnapshot = querySnapshot.docs[0]
              let data = docSnapshot.data()
              this.usteps.find(usteps => usteps.uid === this.usteps[this.pointIndex].uid).username =  data.displayName
            }
            let docRef =  doc(db, 'userGroups', this.usteps[this.pointIndex].groupid)
            await getDoc(docRef).then((snapshot) => {
              if (snapshot.exists()) {
                let data = snapshot.data()
                data.id = snapshot.id
                this.usteps.find(usteps => usteps.uid === this.usteps[this.pointIndex].uid).groupname =  data.name
              }
            })
          }
          if(this.pointIndex - 1 >= 0){
            if(this.usteps[this.pointIndex -1].username == ''){
              let ref = collection(db, 'users')
              let q = query(ref, where("userId", "==", this.usteps[this.pointIndex -1].uid))
              let querySnapshot = await getDocs(q)
              if (querySnapshot.docs.length!==0){
                let docSnapshot = querySnapshot.docs[0]
                let data = docSnapshot.data()
                this.usteps.find(usteps => usteps.uid === this.usteps[this.pointIndex -1].uid).username =  data.displayName
              }
              let docRef =  doc(db, 'userGroups', this.usteps[this.pointIndex-1].groupid)
              await getDoc(docRef).then((snapshot) => {
                if (snapshot.exists()) {
                  let data = snapshot.data()
                  data.id = snapshot.id
                  this.usteps.find(usteps => usteps.uid === this.usteps[this.pointIndex-1].uid).groupname =  data.name
                }
              })
          }
        }
          if(this.pointIndex + 1 != this.usteps.length){
            if(this.usteps[this.pointIndex -1].username == null){
              let ref = collection(db, 'users')
              let q = query(ref, where("userId", "==", this.usteps[this.pointIndex +1].uid))
              let querySnapshot = await getDocs(q)
              if (querySnapshot.docs.length!==0){
                let docSnapshot = querySnapshot.docs[0]
                let data = docSnapshot.data()
                this.usteps.find(usteps => usteps.uid === this.usteps[this.pointIndex +1].uid).username =  data.displayName
              }
              let docRef =  doc(db, 'userGroups', this.usteps[this.pointIndex +1].groupid)
              await getDoc(docRef).then((snapshot) => {
                if (snapshot.exists()) {
                  let data = snapshot.data()
                  data.id = snapshot.id
                  this.usteps.find(usteps => usteps.uid === this.usteps[this.pointIndex+1].uid).groupname =  data.name
                }
              })
            }
          }
        }
        'Show GroupName'
        for (let i = 0; i < this.gsteps.length; i++) {
          let docRef =  doc(db, 'userGroups', this.gsteps[i].groupid)
          await getDoc(docRef).then((snapshot) => {
            if (snapshot.exists()) {
              let data = snapshot.data()
              data.id = snapshot.id
              this.gsteps.find(gsteps => gsteps.groupid === this.gsteps[i].groupid).groupname =  data.name
            }
          })
        }
        this.userStepsDisplay = this.usteps
        this.groupStepsDisplay = this.gsteps
      }
    },
  },
  mounted() {
    this.challengeId = this.$route.params.recordId
  },
});
</script>

<style scoped>

</style>
