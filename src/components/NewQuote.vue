<template>
  <v-card color="primary" style="min-height: 500px" class="mx-auto">
    <v-progress-linear indeterminate color="yellow darken-2" :active="loadingBool"></v-progress-linear>
    <div class="row">
      <div class="col" style="padding-left: 50px; padding-top: 40px">
        <v-card class="mx-auto" max-width="344" min-width="100%" min-height="415">
          <v-card-text>
            <p class="display-1 text--primary text-center">
              <v-icon>mdi-folder-plus-outline</v-icon>
            </p>
            <v-divider></v-divider>
            <br />
            <p class="body-1">You're here because you want to submit a new Proposal to us, right?</p>
            <p class="body-1">Hit the button below to get started!</p>
            <br />

            <br />
            <v-dialog v-model="dialog" persistent max-width="600px">
              <template v-slot:activator="{ on }">
                <v-container v-for="align in alignments" :key="align">
                  <p class="text-center">
                    <v-btn class="mx-2" fab dark large color="secondary" v-on="on">
                      <v-icon dark>mdi-pencil</v-icon>
                    </v-btn>
                  </p>
                </v-container>
              </template>

              <v-card>
                <v-card-title>
                  <span class="headline">New Proposal</span>
                </v-card-title>
                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field v-model="proposal.name" color="secondary" label="Full Name"></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="proposal.postcode"
                          color="secondary"
                          label="Postcode"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="proposal.mobile"
                          color="secondary"
                          label="Mobile Number"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12">
                        <v-text-field
                          v-model="proposal.email"
                          color="secondary"
                          label="Email Address"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12">
                        <v-menu
                          color="secondary"
                          v-model="menu2"
                          :close-on-content-click="false"
                          :nudge-right="40"
                          transition="scale-transition"
                          offset-y
                          min-width="290px"
                        >
                          <template v-slot:activator="{ on }">
                            <v-text-field
                              color="secondary"
                              v-model="proposal.dob"
                              label="Date of Birth"
                              readonly
                              v-on="on"
                            ></v-text-field>
                          </template>
                          <v-date-picker
                            color="secondary"
                            v-model="proposal.dob"
                            @input="menu2 = false"
                          ></v-date-picker>
                        </v-menu>
                      </v-col>
                      <v-col cols="12" sm="6">
                        <v-text-field
                          v-model="proposal.advance"
                          color="secondary"
                          prefix="£"
                          label="Advance"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6">
                        <v-text-field
                          v-model="proposal.term"
                          color="secondary"
                          suffix="Months"
                          label="Term"
                        ></v-text-field>
                      </v-col>
                    </v-row>
                  </v-container>
                  <small>Please ensure all the information provided is accurate to ensure a correct decision</small>
                </v-card-text>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="secondary" text @click="dialog = false">Close</v-btn>
                  <v-btn color="secondary" text @click="dialog = false; postProposal()">Save</v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-card-text>
        </v-card>
      </div>
      <div class="col text-center">
        <v-container fill-height fluid>
          <v-row align="center" justify="center">
            <v-col>
 <v-btn v-if="viewButton" class="mx-2" fab dark x-large color="secondary">
      <v-icon dark>mdi-arrow-right-bold-circle-outline</v-icon>
    </v-btn>

            </v-col>
          </v-row>
        </v-container>
      </div>
    </div>
    <v-snackbar v-model="snackbar">
      {{ text }}
      <v-btn color="pink" text @click="snackbar = false">Close</v-btn>
    </v-snackbar>
  </v-card>
</template>

<script>
import { eventBus } from '../main';
export default {
  data: function() {
    return {
      date: new Date().toISOString().substr(0, 10),
      menu: false,
      modal: false,
      menu2: false,
      loadingBool: false,
      urlStem: "http://publicduck.online:5050/api",
      dialog: false,
      alignments: ["center"],
      snackbar: false,
      text: "",
      viewButton: false,
      activeProposalNow: '',
      proposal: {
        name: "",
        mobile: "",
        postcode: "",
        email: "",
        dob: null,
        advance: null,
        term: null,
        brokerId: this.brokerId
      },
      response: ""
    };
  },
  methods: {
    postProposal: async function() {
      this.loadingBool = true;
      const rawResponse = await fetch(this.urlStem + "/proposals", {
        method: "POST",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json"
        },
        body: JSON.stringify(this.proposal)
      });

      const content = await rawResponse.json();
      console.log(content.action);

      setTimeout(() => {
        if (content.action == "proposalFailed") {
          this.text = "Failed to Submit, please check the details and try again";
          this.snackbar = true
          this.loadingBool = false
        } else {
            this.text = "Proposal Submitted Successfully. Hit the arrow button to view.";
            this.snackbar = true
            this.loadingBool = false
            this.viewButton = true
            this.activeProposalNow = content.proposal.proposalId
            console.log(content.proposal)
            console.log(this.activeProposalNow)
            eventBus.$emit('proposalSelected', this.activeProposalNow)
            }
      }, 2000);
        
      this.response = content.body;
    }
  },
  created() {
    this.$emit("titleChanged", "New Proposal");

    console.log("quotes");
  },
  deactivated() {
    console.log("deactivated");
  },
  props: ["pgTitle", "brokerId", "activeProposal"]
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

.infield {
  padding-left: 30px;
  padding-right: 30px;
  padding-top: 30px;
}
</style>