<template>
  <v-card color="primary" style="min-height: 500px" class="mx-auto white--text">
    <div style="min-height: 100%" class="row">
      <div class="col" style="inherit">
        <p class="display-3 space text-center">
          <span class="font-weight-regular">Register</span>
          <span class="font-weight-thin"> Below</span>
          
        </p>
        <v-divider dark></v-divider>
        <v-card
          color="white"
          style="max-width: 96%; padding: 10px; margin: 25px; min-height: 72%"
          class="mx-auto dark--text"
        >
          <div style="min-height: 95%" class="row">
            <div class="col">
              <v-row justify="center">
                <div class="col-8 text-center" align-center>
                  <span>
                    <v-text-field
                      v-model="brokerInput"
                      color="amber darken-2"
                      class="infield"
                      label="BrokerID"
                    ></v-text-field>
                    <v-text-field
                      v-model="passwordInput"
                      color="amber darken-2"
                      class="infield"
                      label="Password"
                      type="password"
                      @keydown.enter="createBroker"
                    ></v-text-field>
                    <br />
                    <v-btn @click="createBroker()" class="ma-2" tile outlined color="secondary">
                      <v-icon left>mdi-plus</v-icon>Create
                    </v-btn>
                  </span>
                </div>
              </v-row>
            </div>
          </div>
        </v-card>
      </div>
    </div>
    <div class="row">
      <div class="col"></div>
    </div>
            <v-snackbar v-model="snackbar" :timeout="timeout">
      Unable to login! Check your details and try again
      <v-btn color="secondary" text @click="snackbar = false">Close</v-btn>
    </v-snackbar>
  </v-card>
</template>

<script>
import { eventBus } from "../main.js";

export default {
  data: function() {
    return {
      brokerInput: '',
      passwordInput: '',
      snackbar: false,
      urlStem: "http://publicduck.online:5050/api",
      timeout: 5000
    };
  },
  methods: {
    clearBrokerId: function() {
      this.snackbar = true;
      eventBus.$emit("brokerIdUpdated", "");
    },
    registerBrokerId: function() {
      eventBus.$emit("brokerIdUpdated", this.brokerInput);
      console.log(this.brokerInput);
    },
    logBroker() {
      console.log(this.brokerId);
    },
    createBroker: async function() {
        let request = {
          brokerId: this.brokerInput,
          password: this.passwordInput
        }
        let fetchUrl = this.urlStem + '/broker/new'
        let response = await fetch(fetchUrl, {
        method: "POST",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json"
        },
        body: JSON.stringify(request),
        cors: true
      });

      let data = await response.json()

      if (!data.error) {
        console.log(data)
        eventBus.$emit("brokerIdUpdated", data.broker.brokerId);
        eventBus.$emit('activeState', 'myActiveQuotes')
      } else {
        this.snackbar = true
      }
    }
  },
  created() {
    this.$emit("titleChanged", "Register");
    console.log("home");
  },
  deactivated() {
    console.log("deactivated");
  },
  props: ["pgTitle", "brokerId"]
};
</script>

<style>
.space {
  padding: 20px;
}

p {
  padding-left: 25px;
  padding-right: 25px;
}
</style>