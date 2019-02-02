<template>
  <v-container grid-list-md>
    <v-layout column>
      <v-layout row>
        <v-flex xs6>
          <v-text-field 
            type="number"
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
                <h4 class="text-uppercase">Deadlift</h4>
                 <v-text-field 
                    v-bind:class="buttonTextSize"
                    type="number"
                    v-model="deadliftWeight"
                    >
                  </v-text-field>
            </v-card-title>
          </v-card>
        </v-flex>
        <v-flex xs4>
          <v-card dark color="primary"> 
            <v-card-title primary-title class="justify-center" >
              <h4 class="text-uppercase">Bench</h4>
                 <v-text-field
                    v-bind:class="buttonTextSize"
                    type="number"
                    v-model="benchWeight"
                    >
                  </v-text-field>
            </v-card-title>
          </v-card>
        </v-flex>
        <v-flex xs4>
          <v-card dark color="primary"> 
            <v-card-title primary-title class="justify-center" >
              <h4 class="text-uppercase">Clean</h4>
                 <v-text-field
                    v-bind:class="buttonTextSize"
                    type="number"
                    v-model="cleanWeight"
                    >
                  </v-text-field>
            </v-card-title>
          </v-card>
        </v-flex>
      </v-layout>
    </v-layout>
    <v-layout row style="padding-top: 0px;">
      <v-flex>
        <v-card >
          <v-card-title primary-title>
            <div>
              <div class="headline">Instructions</div>
              <ol>
                <li>Enter your body weight</li>
                <li>Select workout scale</li>
                <li>Modify weights if necessary</li>
                <li class="hidden-sm-and-down">Setup 3 bars</li>
              </ol>
            </div>
          </v-card-title>
          <v-card-actions>
            <v-btn 
              flat color="orange"
              @click="setupComplete">
              Continue to Workout
            </v-btn>
        </v-card-actions>
        </v-card>
      </v-flex>
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
    { text: 'Rx', value: '1' },
    { text: 'Intermediate', value: '2' },
    { text: 'Beginner', value: '3' },
  ];

  constructor() {
    super();
    this.recalculate();
  }

  private get buttonTextSize() {
    let rval: string;

    switch (this.$vuetify.breakpoint.name) {
        case 'xs':
        case 'sm': rval = "headline";
        break;
        case 'md': 
        case 'lg':
        case 'xl': 
        default: rval = "display-1";
        break;
      }

      return(rval);
  }

  private changeScale(newItem: string) {
    this.$emit('scale-changed', newItem);
    this.selectedScale = newItem;
    this.recalculate();
  }

  private recalculate() {
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

  private setupComplete() {
    /*
      setup complete
    */
    this.$emit('setup-complete'); // finished setup
  }
}
</script>

<style>
</style>
