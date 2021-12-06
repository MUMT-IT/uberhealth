<template>
  <ion-page>
    <ion-content>
      <ion-grid>
        <ion-row>
          <ion-col>
            <ion-text class="ion-text-lg-center">
              <h1>Ranking</h1>
              <p>ชื่อ Challenge</p>
            </ion-text>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-list>
            {{ userStep }}
          </ion-list>
        </ion-row>
        <ion-row>
          {{ groupNameStep }}
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
  IonList
} from '@ionic/vue';
import {defineComponent} from 'vue';
import {mapState} from "vuex";
import { db } from '@/firebase'
import { doc } from '@firebase/firestore'
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
    IonList
  },
  data(){
    return{
      userSteps:{
        userId:'',
        steps: 0,
      },
      groupsSteps:{
        groupsId:'',
        steps: 0,
      },
      challengeId: '',
      userGroupsId: null,
      userMembers: {},
      userStep: {},
      groupNameStep: {}
    }
  },
  computed: {
    ...mapState(['user','profile','challenges'])
  },
  watch:{
    challengeId: async function() {
      await this.LoadGroupsSteps()
    }
  },
  methods:{
    async LoadGroupsSteps(){
      let docRef = await doc(db, 'challenges', this.challengeId)
      getDoc(docRef).then((snapshot) => {
        if (snapshot.exists()) {
          let data = snapshot.data()
          data.id = snapshot.id
          this.userGroupsId = data.groups
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
      console.log(this.groupNameStep)
    },
    async Loadactivity_records(recordId,uId,groupName){
      let docRef =  doc(db, 'activity_records', recordId)
      await getDoc(docRef).then((snapshot) => {
        if (snapshot.exists()) {
          let data = snapshot.data()
          data.id = snapshot.id
          'user Step'
          if(this.userStep[uId] === undefined){
            this.userStep[uId] = parseInt(data.steps)
          }else{
            this.userStep[uId] += parseInt(data.steps)
          }
          'GroupName Step'
          if(this.groupNameStep[groupName] === undefined){
            this.groupNameStep[groupName] = parseInt(data.steps)
          }else{
            this.groupNameStep[groupName] += parseInt(data.steps)
          }
        }
      })
    }
  },
  updated() {
    this.challengeId = this.$route.params.recordId
  },
});
</script>

<style scoped>

</style>
