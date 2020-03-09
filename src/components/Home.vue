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
          style="max-width: 96%; padding: 25px; margin: 25px; min-height: 70%"
          class="mx-auto dark--text"
        >
          <div style="min-height: 95%" class="row">
            <div class="col">
              <br />
              <p class="body-1">Welcome to Engager, our brand new broker portal.</p>
              <p class="body-1">
                If you have questions, check out the FAQ or submit any technical
                issues to
                our Technical Support Desk.
              </p>
              <p class="body-1">
                We're proud of the platform we've built with Engager, but we
                understand
                that many of our partners choose to use their own in house platform. For that
                reason,
                all of the facilities we offer here (and more!) are also accesible via our new
                API.
                You
                can see the full specification on our API Documentation.
              </p>
              <br />
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