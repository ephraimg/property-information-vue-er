<template>
  <v-app>
    <v-toolbar fixed app>
      <v-layout column align-center>
        <v-toolbar-title>
          <span>{{address}}</span>
        </v-toolbar-title>
      </v-layout>
    </v-toolbar>
    <v-content>
      <v-container fluid>
        <v-layout column align-center>
          <img src="/public/v.png" alt="Vuetify.js" class="mb-5" />

          <h2>Property information</h2>
            <div @click.prevent="toggleDetails" :style="{ cursor: 'pointer'}">
            <span v-if="!showingDetails">View details</span>
            <span v-if="showingDetails">Hide details</span>
            <br><br>
          </div>

          <ul v-if="!showingDetails">
            <li>Bedrooms: {{ property.number_of_bedrooms || 'Unknown' }}</li>
            <li>Bathrooms: {{ property.total_bath_count || 'Unknown' }}</li>
            <li>Area sq ft: {{ property.building_area_sq_ft || 'Unknown' }}</li>
            <li>Value: {{ assessment.total_assessed_value ? inDollars(assessment.total_assessed_value || 0) : 'Unknown' }}</li>
          </ul>
          
          <ul v-if="showingDetails">
            <li v-for="(value, key) in details" :key="key">{{key}}: {{value}}</li>
          </ul>

        </v-layout>
      </v-container>
    </v-content>
    <v-footer fixed app>
      &nbsp;&nbsp;<a href="http://www.housecanary.com" target="_blank" style="font-family: Avenir, Lucida Sans, Verdana, Arial, Helvetica, sans-serif !important; font-size: 14px !important; line-height: 1 !important; color: #3c555c !important; letter-spacing: 0px !important; text-align: left !important; text-decoration: none !important; font-weight: normal !important;">Powered by <img src="https://s3-us-west-2.amazonaws.com/hc-pro-images/app/housecanary-attribution_logo%402x-1.png" alt="HouseCanary" title="HouseCanary" style="display: inline-block !important; width: 127px !important; height: 19px !important; margin-left: 6px !important;" /></a>
    </v-footer>
  </v-app>
</template>

<script>
  import axios from 'axios';
  export default {
    data () {
      return {
        API_KEY: 'XJEN8GDDUT0AEHY41SYR',
        API_SECRET: 'CMpJLhlCJrDHujL82hgy5b6EgVvnA10X',
        address: '1427 12th St, Oakland, CA 94607',
        fixed: true,
        showingDetails: false,
        "property/details": {
          "result": {
            "property": {
              "size": 4,
              "color": 'blue'
            },
            "assessment": {
              "value": 123424125
            }
          }
        },
      }
    },
    computed: {
      property: function() {
        return this["property/details"].result.property;
      },
      assessment: function() {
        return this["property/details"].result.assessment;
      },
      details: function() {
        const combined = {};
        const clean = key => key.split('_').map(word => {
          return word[0].toUpperCase() + word.slice(1)
        }).join(' ');
        for (let key in this.property) {
          combined[clean(key)] = this.property[key] || 'Unknown'
        }
        for (let key in this.assessment) {
          combined[clean(key)] = this.assessment[key] || 'Unknown';
        }
        combined["Tax Amount"] = this.inDollars(combined["Tax Amount"]);
        combined["Total Assessed Value"] = this.inDollars(combined["Total Assessed Value"]);
        return combined;
      }
    },
    methods: {
      inDollars (value) {
        return (new Intl.NumberFormat('en-US', {
          style: 'currency',
          currency: 'USD',
          minimumFractionDigits: 0
        })).format(value);
      },
      toggleDetails () {
        this.showingDetails = !this.showingDetails;
      }
    },
    created: function () {
      axios({
        method: 'get',
        url: 'https://cors-anywhere.herokuapp.com/https://api.housecanary.com/v2/property/details?address=1427+12th+St&zipcode=94607',
        auth: {
            username: this.API_KEY,
            password: this.API_SECRET 
        }
      }).then(res => {
        this["property/details"] = res.data[0]["property/details"];
      });
    }
  }
</script>
