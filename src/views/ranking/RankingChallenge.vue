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
          <ion-col>
            <ion-list>
              <ion-list-header>
                <h4>You</h4>
              </ion-list-header>
              <ion-item v-for="(ustep,index) in usteps.slice(CaculateUpindex,pointIndex+2)" :key="ustep.id">
                <ion-col>
                  <ion-label>
                    {{ pointIndex + index }}
                  </ion-label>
                </ion-col>
                <ion-col size="7">
                  <ion-label>{{ ustep.name }}
                    <p>
                      {{ ustep.group }}
                    </p>
                  </ion-label>
                </ion-col>
                <ion-col>
                  <ion-label class="ion-text-right">{{ ustep.steps }}</ion-label>
                </ion-col>
                <ion-col>
                  <ion-label class="ion-text-right">Steps</ion-label>
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
            <ion-item v-for="(gstep,index) in gsteps" :key="gstep.id">
              <ion-col>
                <ion-label>
                  {{ index+1 }}
                </ion-label>
              </ion-col>
              <ion-col size="7">
                <ion-label>{{ gstep.group }}</ion-label>
              </ion-col>
              <ion-col>
                <ion-label class="ion-text-right">{{ gstep.steps }}</ion-label>
              </ion-col>
              <ion-col>
                <ion-label class="ion-text-right">Steps</ion-label>
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
              <ion-item v-for="(ustep,index) in usteps.slice(0,10)" :key="ustep.id">
                <ion-col>
                  <ion-label>
                    {{ index+1 }}
                  </ion-label>
                </ion-col>
                <ion-col size="7">
                  <ion-label>{{ ustep.name }}
                  <p>
                    {{ ustep.group }}
                  </p>
                  </ion-label>
                </ion-col>
                <ion-col>
                  <ion-label class="ion-text-right">{{ ustep.steps }}</ion-label>
                </ion-col>
                <ion-col>
                  <ion-label class="ion-text-right">Steps</ion-label>
                </ion-col>
              </ion-item>
            </ion-list>
          </ion-col>
        </ion-row>
        <ion-row>
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
  IonList,
  IonLabel,
  IonListHeader,
  IonFabButton,
  IonFab,
  IonIcon
} from '@ionic/vue';
import {defineComponent} from 'vue';
import { arrowBackCircle } from 'ionicons/icons'
import {mapState} from "vuex";
import { db } from '@/firebase';
import { doc } from '@firebase/firestore';
import {getDoc,collection,where,getDocs,query} from "firebase/firestore";
export default defineComponent({
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
    IonIcon
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
      gsteps:[],
      challengeId: '',
      userGroupsId: null,
      userMembers: {},
      userStep: {},
      groupNameStep: {},
      challengeTitle: '',
    }
  },
  computed: {
    ...mapState(['user','profile','challenges']),
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
      this.ResetData()
      await this.LoadGroupsSteps()

    }
  },
  methods:{
    ResetData(){
      this.userStep = {}
      this.groupNameStep = {}
      this.usteps = []
      this.gsteps =[]
    },
    async LoadGroupsSteps(){
      let docRef = await doc(db, 'challenges', this.challengeId)
      getDoc(docRef).then((snapshot) => {
        if (snapshot.exists()) {
          let data = snapshot.data()
          data.id = snapshot.id
          this.userGroupsId = data.groups
          this.challengeTitle = data.title
          this.LoaduserGroups(this.userGroupsId)
        }
      })
      },
    async LoaduserGroups(userGroupsId){
      for (let i = 0; i < userGroupsId.length; i++) {
        let docRef = await doc(db, 'userGroups',userGroupsId[i])
        getDoc(docRef).then((snapshot) => {
          if (snapshot.exists()) {
            let data = snapshot.data()
            data.id = snapshot.id
            this.Loadactivity_submission(data)
          }
        })
      }
    },
    async Loadactivity_submission(usermembesrs){
      for (let i = 0; i < usermembesrs.members.length; i++) {
        let ref = collection(db, 'activity_submission')
        let q = query(ref, where("userId", "==", usermembesrs.members[i]), where('challengeId', '==',this.challengeId ))
        let querySnapshot = await getDocs(q)
        querySnapshot.forEach(d => {
          let data = d.data()
          data.id = d.id
          this.Loadactivity_records(data.recordId,usermembesrs.members[i],usermembesrs.name)
        })
      }
    },
    async Loadactivity_records(recordId,uId,groupName){
      let usteps = null
      let docRef =  doc(db, 'activity_records', recordId)
      await getDoc(docRef).then((snapshot) => {
        if (snapshot.exists()) {
          let data = snapshot.data()
          data.id = snapshot.id
          usteps = data.steps
        }
      })

      let ref = collection(db, 'users')
      let q = query(ref, where("userId", "==", uId))
      let querySnapshot = await getDocs(q)
      if (querySnapshot.docs.length!==0){
        let docSnapshot = querySnapshot.docs[0]
        let data = docSnapshot.data()
        let userName =  data.displayName

        'user Step'
        if(this.userStep[uId] === undefined){
          this.userStep[uId] = parseInt(usteps)
          this.usteps.push({
            uid: uId,
            name: userName,
            steps: usteps,
            group: groupName
          })
        }else{
          this.userStep[uId] += parseInt(usteps)
          this.usteps.find(usteps => usteps.uid === uId).steps = parseInt(this.userStep[uId])
        }
        'GroupName Step'
        if(this.groupNameStep[groupName] === undefined){
          this.groupNameStep[groupName] = parseInt(usteps)
          this.gsteps.push({
            group: groupName,
            steps: usteps,
          })
        }else{
          this.groupNameStep[groupName] += parseInt(usteps)
          this.gsteps.find(gsteps => gsteps.group === groupName).steps = parseInt(this.groupNameStep[groupName])
        }
        this.usteps.sort((a,b)=> b.steps-a.steps)
        this.gsteps.sort((a,b)=> b.steps-a.steps)
        this.pointIndex = this.usteps.findIndex(usteps => usteps.uid === this.$store.state.user.userId)
        if(this.pointIndex == 0 ){
          this.pointIndex = 1
        }
      }
    },
  },
  updated() {
    this.challengeId = this.$route.params.recordId
  },
});
</script>

<style scoped>

</style>
