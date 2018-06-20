<template>
  <v-app>
    <v-navigation-drawer
      fixed
      :mini-variant="miniVariant"
      :clipped="clipped"
      v-model="drawer"
      app
    >
      <v-list>
        <v-list-tile 
          value="true"
          v-for="(item, i) in items"
          :key="i"
        >
          <v-list-tile-action>
            <v-icon v-html="item.icon"></v-icon>
          </v-list-tile-action>
          <v-list-tile-content>
            <v-list-tile-title v-text="item.title"></v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer>
    <v-toolbar fixed app :clipped-left="clipped">
      <v-toolbar-side-icon @click.stop="drawer = !drawer"></v-toolbar-side-icon>
      <v-btn icon @click.stop="miniVariant = !miniVariant">
        <v-icon v-html="miniVariant ? 'chevron_right' : 'chevron_left'"></v-icon>
      </v-btn>
      <v-btn icon @click.stop="clipped = !clipped">
        <v-icon>web</v-icon>
      </v-btn>
      <v-btn icon @click.stop="fixed = !fixed">
        <v-icon>remove</v-icon>
      </v-btn>
      <v-toolbar-title>
        <span>{{title + address}}</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn icon @click.stop="rightDrawer = !rightDrawer">
        <v-icon>menu</v-icon>
      </v-btn>
    </v-toolbar>
    <v-content>
      <v-container fluid>
        <v-slide-y-transition mode="out-in">
          <v-layout column align-center>
            <img src="/public/v.png" alt="Vuetify.js" class="mb-5" />
            <h2>Essential details</h2>
            <ul>
              <li>
                Bedrooms: {{ property ? property.number_of_bedrooms : 'Unknown' }}
              </li>
              <li>
                Bathrooms: {{ property ? property.total_bath_count : 'Unknown' }}
              </li>
              <li>
                Area: {{ property ? property.building_area_sq_ft + 'sq ft' : 'Unknown' }}
              </li>
              <li>
                Value: {{ assessment ? inDollars(assessment.total_assessed_value) : 'Unknown' }}
              </li>
            </ul>
            <br>
            <span v-if="!showingFullDetails" @click.prevent="showFullDetails">For full details, click here</span>
            <ul v-if="showingFullDetails">
              {{ fullDetails.map(detail => <li>hi</li> }}
            </ul>
          </v-layout>
        </v-slide-y-transition>
      </v-container>
    </v-content>
    <v-navigation-drawer
      temporary
      :right="right"
      v-model="rightDrawer"
      fixed
    >
      <v-list>
        <v-list-tile @click.native="right = !right">
          <v-list-tile-action>
            <v-icon>compare_arrows</v-icon>
          </v-list-tile-action>
          <v-list-tile-title>Switch drawer (click me)</v-list-tile-title>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer>
    <v-footer :fixed="fixed" app>
      <span>&nbsp;&copy; 2017&nbsp;&nbsp;</span>
      <a href="http://www.housecanary.com" target="_blank" style="font-family: Avenir, Lucida Sans, Verdana, Arial, Helvetica, sans-serif !important; font-size: 14px !important; line-height: 1 !important; color: #3c555c !important; letter-spacing: 0px !important; text-align: left !important; text-decoration: none !important; font-weight: normal !important;">Powered by <img src="https://s3-us-west-2.amazonaws.com/hc-pro-images/app/housecanary-attribution_logo%402x-1.png" alt="HouseCanary" title="HouseCanary" style="display: inline-block !important; width: 127px !important; height: 19px !important; margin-left: 6px !important;" /></a>
    </v-footer>
  </v-app>
</template>

<script>
  import axios from 'axios';


  const dollarFormat = new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: 'USD',
    minimumFractionDigits: 0
  });
  export default {
    data () {
      return {
        API_KEY: 'XJEN8GDDUT0AEHY41SYR',
        API_SECRET: 'CMpJLhlCJrDHujL82hgy5b6EgVvnA10X',
        address: '1427 12th St, Oakland, CA 94607',
        clipped: false,
        drawer: true,
        fixed: false,
        items: [
          { icon: 'bubble_chart', title: 'Inspire' }
        ],
        miniVariant: false,
        right: true,
        rightDrawer: false,
        title: 'Property information for ',

        "property/details": {
          "result": {}
        },

        showingFullDetails: false,
        fullDetails: {'detail 1': 'value1', 'detail 2': 'value2'}

      }
    },
    computed: {
      property: function() {
        return this["property/details"].result.property;
      },
      assessment: function() {
        return this["property/details"].result.assessment;
      }
    },
    methods: {
      inDollars (value) {
        return dollarFormat.format(value);
      },
      showFullDetails () {
        this.showingFullDetails = !this.showingFullDetails;
        console.log('clicked');
      }
    },
    // created: function () {
    // // `this` points to the vm instance
    //   axios({
    //     method: 'get',
    //     url: 'https://cors-anywhere.herokuapp.com/https://api.housecanary.com/v2/property/details?address=1427+12th+St&zipcode=94607',
    //     auth: {
    //         username: this.API_KEY,
    //         password: this.API_SECRET 
    //     }
    //   }).then(res => {
    //     console.log(res.data[0]);
    //     this["property/details"] = res.data[0]["property/details"];
    //   });
  // },
  }
</script>
