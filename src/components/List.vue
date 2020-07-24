<template>
  <v-col cols="12">
      <v-card>
          <v-row>
              <v-col cols="12">
                {{this.data.name}}
              </v-col>
              <v-col cols="12">
                {{this.data.deadline}}
              </v-col>
              <v-col cols="12">
                  <v-btn class="success" @click="finish()">
                      FINISH
                  </v-btn>
              </v-col>
          </v-row> 
      </v-card>
  </v-col>
</template>

<script>
export default {
    props:{
        data: Object
    },
    methods:{
        finish(){
            this.$emit("finish",this.data.key)
        }
    }
}
</script>

<style>

</style>