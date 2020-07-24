<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12">
        <h1>To Do App</h1>
      </v-col>
      <v-col cols="6">
        <v-text-field v-model="info.name" outlined label="Work"></v-text-field>
      </v-col>
      <v-col cols="3">
        <v-menu
          ref="menu"
          v-model="menu"
          :close-on-content-click="false"
          transition="scale-transition"
          offset-y
          min-width="290px"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-text-field
              v-model="info.deadline"
              label="Dead Line"
              readonly
              v-bind="attrs"
              v-on="on"
            ></v-text-field>
          </template>
          <v-date-picker v-model="info.deadline" no-title scrollable>
            <v-spacer></v-spacer>
            <v-btn text color="primary" @click="info.deadline='';menu = false">Cancel</v-btn>
            <v-btn text color="primary" @click="$refs.menu.save()">OK</v-btn>
          </v-date-picker>
        </v-menu>
      </v-col>
      <v-col cols="2">
        <v-select label="Type" :items="type" v-model="info.type"></v-select>
      </v-col>
      <v-col cols="1">
        <v-btn color="primary" @click="send">ADD</v-btn>
      </v-col>
      <v-col cols="4" style="background:#9CCC65;height:70vh;overflow:auto">
        <v-row>
          <v-col cols="12">
            <h2>Safe Zone</h2>
            <v-row v-for="(dat,index) in safe" :key="index">
              <List :data="dat" @finish="finish"/>
            </v-row>
          </v-col>
        </v-row>
      </v-col>
      <v-col cols="4" style="background:#FFB74D;height:70vh;overflow:auto">
        <v-row>
          <v-col cols="12">
            <h2>Important</h2>
            <v-row v-for="(dat,index) in important" :key="index">
              <List :data="dat" @finish="finish"/>
            </v-row>
          </v-col>
        </v-row>
      </v-col>
      <v-col cols="4" style="background:#EF5350;height:70vh;overflow:auto">
        <v-row>
          <v-col cols="12">
            <h2>Emergency</h2>
            <v-row v-for="(dat,index) in emergency" :key="index">
              <List :data="dat" @finish="finish"/>
            </v-row>
          </v-col>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import List from './List'
import firebase from 'firebase'
const firebaseConfig = {
};
firebase.initializeApp(firebaseConfig)
var database = firebase.database()
var todoRef = database.ref('/')
  export default {
    name: 'Todo',
    data(){
      return{
        menu: false,
        data:{name:"test",deadline:"01/02/2564"},
        type:["safe","important"],
        info: {
          name: "",
          deadline: "",
          type:"safe",
          finish:false
        },
        safe:[],
        important:[],
        emergency:[]
      }
    },
    components: {
      List
    },
    async mounted(){
      await todoRef.on('value',(snapshot)=>{
        const data1 = snapshot.val()
        this.safe =[];
        this.important=[];
        this.emergency=[];
        if(data1){
        for(let key of Object.keys(data1)){
          let el=data1[key]
          el.key = key
          if(el.type == 'important'){
            if(new Date(el.deadline).getTime() - new Date().getTime() < 259200000){
              this.emergency.push(el)
            }
            else{
              this.important.push(el)
            }
          }
          else{
            if(new Date(el.deadline).getTime() - new Date().getTime() < 259200000){
              this.important.push(el)
            }
            else {
              this.safe.push(el)
            }
          }
        }
          
        }
        
      })
    },
    methods: {
      send(){
        todoRef.push(this.info)
        this.info = {
          name: "",
          deadline: "",
          type:"safe",
          finish:false
        }
      },
      finish(key){
        todoRef.child(key).remove()
      }
    }
  }
</script>
