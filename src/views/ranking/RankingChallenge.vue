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
          <ion-col>
            <ion-item v-for="(vaule,name) in groupNameStep" :key="name.id">
              <ion-col>
                <ion-label>{{ name }}</ion-label>
              </ion-col>
              <ion-col>
                <ion-label class="ion-text-right">{{ vaule }}</ion-label>
              </ion-col>
            </ion-item>
          </ion-col>
        </ion-row>
        <ion-row>
          <ion-col>
            <ion-list>
              <ion-item v-for="ustep in usteps" :key="ustep.id">
                <ion-col>
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
                  <ion-label class="ion-text-right">ก้าว</ion-label>
                </ion-col>
              </ion-item>
            </ion-list>
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
  IonLabel
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
    IonList,
    IonLabel
  },
  data(){
    return{
      usteps:[],
      challengeId: '',
      userGroupsId: null,
      userMembers: {},
      userStep: {},
      groupNameStep: {}
    }
  },
  computed: {
    ...mapState(['user','profile','challenges']),
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
          this.usteps.find(usteps => usteps.uid === uId).steps = this.userStep[uId]
        }
        this.usteps.sort((a,b)=> b.steps-a.steps)
        'GroupName Step'
        if(this.groupNameStep[groupName] === undefined){
          this.groupNameStep[groupName] = parseInt(usteps)
        }else{
          this.groupNameStep[groupName] += parseInt(usteps)
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
