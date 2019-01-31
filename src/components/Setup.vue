<template>
  <v-container grid-list-md>
    <v-layout column>
      <v-layout row>
        <v-flex xs6>
          <v-text-field 
            v-model="weight"
            label="Body Weight"
            required
            v-on:change="recalculate"></v-text-field>
        </v-flex>
        <v-flex xs6>
          <v-select
            :items="scaleItems"
            item-text="text"
            item-value="value"
            v-model="selectedScale"
            label="Scale"
            v-on:change="changeScale"
          ></v-select>
        </v-flex>
      </v-layout>

      <v-layout row>
         <v-flex xs4>
          <v-card dark color="primary"> 
            <v-card-title primary-title class="justify-center" >
              <div >
                <h4>DEADLIFT</h4>
                <h3>{{deadliftWeight}}</h3>
              </div>
            </v-card-title>
          </v-card>
        </v-flex>
        <v-flex xs4>
          <v-card dark color="primary"> 
            <v-card-title primary-title class="justify-center" >
              <div >
                <h4>BENCH</h4>
                <h3>{{benchWeight}}</h3>
              </div>
            </v-card-title>
          </v-card>
        </v-flex>
        <v-flex xs4>
          <v-card dark color="primary"> 
            <v-card-title primary-title class="justify-center" >
              <div >
                <h4>CLEAN</h4>
                <h3>{{cleanWeight}}</h3>
              </div>
            </v-card-title>
          </v-card>
        </v-flex>
      </v-layout>



    </v-layout>
  </v-container>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component
export default class Control extends Vue {
  private deadliftWeight: number = 0;
  private benchWeight: number = 0;
  private cleanWeight: number = 0;
  private weight: number = 0;
  private selectedScale: string = '1';
  private scaleItems: any = [
    { text: 'Advanced (Rx)', value: '1' },
    { text: 'Intermediate', value: '2' },
    { text: 'Beginner', value: '3' },
  ];

  constructor() {
    super();
    this.recalculate();
  }

  public changeScale(newItem: string) {
    this.selectedScale = newItem;
    this.recalculate();
  }

  public recalculate() {
    let deadliftMultiplier = 1;
    let benchMultiplier = 1;
    let cleanMultiplier = 1;

    if ( this.selectedScale === '1') {
      deadliftMultiplier = 1.5;
      benchMultiplier = 1;
      cleanMultiplier = .75;
    } else if ( this.selectedScale === '2') {
      deadliftMultiplier = 1.25;
      benchMultiplier = .75;
      cleanMultiplier = .50;
    } else if ( this.selectedScale === '3') {
      deadliftMultiplier = .75;
      benchMultiplier = .50;
      cleanMultiplier = .33;
    }

    this.deadliftWeight = Math.round(this.weight * deadliftMultiplier);
    this.benchWeight = Math.round(this.weight * benchMultiplier);
    this.cleanWeight = Math.round(this.weight * cleanMultiplier);
  }
}
</script>

<style>
</style>
