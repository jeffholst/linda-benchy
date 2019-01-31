<template>
  <v-container grid-list-md>
    <v-layout column>
      <v-card flat >
        <v-layout row>
          <v-flex xs3>
            <div>Reps</div>
          </v-flex>
          <v-flex xs3>
            <div>Deadlift</div>
          </v-flex>
          <v-flex xs3>
             <div>Bench</div>
          </v-flex>
          <v-flex xs3>
            <div>Clean</div>
          </v-flex>
        </v-layout>
        <v-layout v-for="i in repScheme" :key="i" row>
          <v-flex xs3>
            <div>{{i}}</div>
          </v-flex>
          <v-flex xs3>
            <div>---</div>
          </v-flex>
          <v-flex xs3>
            <div>---</div>
          </v-flex>
          <v-flex xs3>
            <div>---</div>
          </v-flex>
        </v-layout>
      </v-card>
    </v-layout>
  </v-container>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component
export default class Control extends Vue {
   private repScheme: number[] = [10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ];
}
</script>

<style>
</style>
