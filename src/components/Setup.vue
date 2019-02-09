<template>
  <v-container grid-list-md fluid>
    <v-layout row>
      <v-flex xs6>
        <v-text-field 
          type="number"
          v-model="weight"
          label="Enter body weight"
          placeholder=" "
          required
          v-on:change="recalculate"></v-text-field>
        </v-flex>
        <v-flex xs6>
          <v-select
            :items="scaleItems"
            item-text="text"
            item-value="value"
            v-model="selectedScale"
            label="Select scale"
            v-on:change="changeScale"
          ></v-select>
        </v-flex>
      </v-layout>
      <v-layout row>
         <v-flex xs4>
          <v-card dark color="primary"> 
            <v-card-title primary-title class="justify-center" >
                <h4 class="text-uppercase">Dead</h4>
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
    <v-layout row style="padding-top: 40px;" justify-center>
   
        <div class="text-xs-center">
        <v-btn
          class="white--text"
          :disabled="disableContinueButton"
          round
          color="orange"
          @click="setupComplete">Continue to Workout
        </v-btn>
      </div>
    </v-layout>
  </v-container>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component
export default class Control extends Vue {
  private disableContinueButton: boolean = true;
  private deadliftWeight: number = 0;
  private benchWeight: number = 0;
  private cleanWeight: number = 0;
  private weight: string = '';
  private selectedScale: string = '1';
  private scaleItems: any = [
    { text: 'Rx', value: '1' },
    { text: 'Intermediate', value: '2' },
    { text: 'Beginner', value: '3' },
  ];

  constructor() {
    super();
  }

  private get buttonTextSize() {
    let rval: string;

    switch (this.$vuetify.breakpoint.name) {
        case 'xs':
        case 'sm':
          rval = 'subheading';
          break;
        case 'md':
        case 'lg':
        case 'xl':
        default:
          rval = 'display-1';
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

    if (this.weight) {
      this.disableContinueButton = false;
    }

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

    this.deadliftWeight = Math.round(parseInt(this.weight, 10) * deadliftMultiplier);
    this.benchWeight = Math.round(parseInt(this.weight, 10) * benchMultiplier);
    this.cleanWeight = Math.round(parseInt(this.weight, 10) * cleanMultiplier);
  }

  private setupComplete() {
    /*
      setup complete
    */
    this.$emit('setup-complete', this.deadliftWeight, this.benchWeight, this.cleanWeight); // finished setup
  }
}
</script>

<style>
</style>
