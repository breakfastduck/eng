<template>
  <v-card color="primary" style="min-height: inherit" class="mx-auto white--text">
    <div style="min-height: 100%" class="row">
      <div class="col" style="inherit">
        <p class="display-3 space text-center">
          <span class="font-weight-thin">Simplicity.</span>
          <span class="font-weight-light">Power.</span>
          <span class="font-weight-regular">Results.</span>
        </p>
        <v-divider dark></v-divider>
        <v-card
          color="white"
          style="max-width: 96%; padding: 25px; margin: 25px; min-height: 60%"
          class="mx-auto dark--text"
        >
          <div style="min-height: 95%" class="row">
            <div class="col">
              
              <v-row justify="center">
                <div class="col-8 text-center" align-center>
                  <v-text-field
                    v-model="brokerInput"
                    color="amber darken-2"
                    class="infield"
                    label="Input BrokerID"
                    @keydown.enter="registerBrokerId()"
                  ></v-text-field>
                </div>
              </v-row>
            </div>
          </div>
        </v-card>
      </div>
    </div>
    <div class="row">
      <div class="col">
      </div>
    </div>
  </v-card>
</template>

<script>
import { eventBus } from "../main.js";

export default {
  data: function() {
    return {
      brokerInput: ""
    };
  },
  methods: {
    registerBrokerId: function() {
      eventBus.$emit('brokerIdUpdated', this.brokerInput);
      console.log(this.brokerInput)
    },
    logBroker() {
      console.log(this.brokerId)
    }
  },
  created() {
    this.$emit("titleChanged", "Home");
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