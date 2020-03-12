<template>
  <v-card color="primary" style="min-height: 630px" class="mx-auto">
    <v-progress-linear indeterminate color="yellow darken-2" :active="loadingBool"></v-progress-linear>

    <div class="row">
      <div class="col" style="padding-left: 50px; padding-right: 50px; padding-top: 40px">
        <v-card color="white" class="mx-auto" max-width="344" min-width="100%" max-height="35">
          <v-card-text>
            <span class="title">
              <v-tooltip bottom>
                <template v-slot:activator="{ on }">
                  <v-btn
                    @click="notesDialogue = true; loadNotes()"
                    class="mx-2"
                    color="secondary"
                    fab
                    x-small
                    dark
                    v-on="on"
                  >
                    <v-icon x-small>mdi-note</v-icon>
                  </v-btn>
                </template>
                <span>View Notes</span>
              </v-tooltip>

              <v-tooltip bottom>
                <template v-slot:activator="{ on }">
                  <v-btn
                    @click="notePost = true; loadNotes()"
                    class="mx-2"
                    color="secondary"
                    fab
                    x-small
                    dark
                    v-on="on"
                  >
                    <v-icon x-small>mdi-file-plus</v-icon>
                  </v-btn>
                </template>
                <span>Post Note</span>
              </v-tooltip>

              <v-tooltip bottom>
                <template v-slot:activator="{ on }">
                  <v-btn
                    @click="ruleShow = true; loadRules()"
                    class="mx-2"
                    color="secondary"
                    fab
                    x-small
                    dark
                    v-on="on"
                  >
                    <v-icon x-small>mdi-alarm-light</v-icon>
                  </v-btn>
                </template>
                <span>View Rules</span>
              </v-tooltip>

              <v-dialog v-model="notesDialogue" persistent max-width="1100px">
                <v-card>
                  <v-card-title>
                    <span class="headline">
                      <v-icon>mdi-account-details</v-icon>Notes
                    </span>
                  </v-card-title>
                  <v-card-text>
                    <v-container>
                      <v-row>
                        <v-col>
                          <v-expansion-panels>
                            <v-expansion-panel v-bind:key="note" v-for="note in notes">
                              <v-expansion-panel-header>Posted At: {{note.createdAt}}</v-expansion-panel-header>
                              <v-expansion-panel-content>{{note.noteBody}}</v-expansion-panel-content>
                            </v-expansion-panel>
                          </v-expansion-panels>
                        </v-col>
                      </v-row>
                    </v-container>
                    <small></small>
                  </v-card-text>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn
                      color="secondary"
                      text
                      @click="notesDialogue = false; text = 'Notes'"
                    >Done</v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>

              <v-dialog v-model="notePost" persistent max-width="1100px">
                <v-card>
                  <v-card-title>
                    <span class="headline">
                      <v-icon>mdi-account-details</v-icon>Add Note
                    </span>
                  </v-card-title>
                  <v-card-text>
                    <v-container>
                      <v-row>
                        <v-col>
                          <v-textarea
                            v-model="noteBody"
                            color="secondary"
                            name="input-7-1"
                            label="Note Text"
                            hint="Write your note above"
                            @keydown.enter="notePost = false; postNote(); text = 'Notes'"
                          ></v-textarea>
                        </v-col>
                      </v-row>
                    </v-container>
                    <small></small>
                  </v-card-text>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn
                      color="secondary"
                      text
                      @click="notePost = false; postNote(); text = 'Notes'"
                    >Done</v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>

              <v-dialog v-model="ruleShow" persistent max-width="1100px">
                <v-card>
                  <v-card-title>
                    <span class="headline">
                      <v-icon>mdi-alarm-light</v-icon> Rules
                    </span>
                  </v-card-title>
                  <v-card-text>
                    <v-container>
                      <v-row>
                        <v-col>
                          <v-simple-table>
                            <template v-slot:default>
                              <thead>
                                <tr>
                                  <th class="text-left">Code</th>
                                  <th class="text-left">Name</th>
                                  <th class="text-left">Result</th>
                                  <th class="text-left">Severity</th>
                                </tr>
                              </thead>
                              <tbody>
                                <tr v-for="rule in rules" :key="rule.rulecode">
                                  <td>{{ rule.rulecode }}</td>
                                  <td>{{ rule.rulename }}</td>
                                  <td>{{ rule.ruleresult }}</td>
                                  <td>{{ rule.ruleseverity }}</td>
                                </tr>
                              </tbody>
                            </template>
                          </v-simple-table>
                        </v-col>
                      </v-row>
                    </v-container>
                    <small></small>
                  </v-card-text>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn
                      color="secondary"
                      text
                      @click="ruleShow = false; text = 'Notes'"
                    >Done</v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>
            </span>
          </v-card-text>
        </v-card>
      </div>
    </div>

    <div class="row">
      <div class="col" style="padding-left: 50px; padding-top: 20px">
        <v-card class="mx-auto" max-width="344" min-width="100%" min-height="415">
          <v-card-text>
            <p class="display-3 text-center">
              <v-icon x-large>mdi-account</v-icon>
              {{ proposal.proposalId }}
            </p>
            <p>
              <span class="title">
                {{ proposal.status }}
                <v-dialog v-model="statusDialogue" persistent max-width="600px">
                  <template v-slot:activator="{ on }">
                    <v-btn class="mx-2" x-small fab dark color="secondary" v-on="on">
                      <v-icon dark>mdi-pencil</v-icon>
                    </v-btn>
                  </template>
                  <v-card>
                    <v-card-title>
                      <span class="headline">
                        <v-icon>mdi-account-details</v-icon>Update Status
                      </span>
                    </v-card-title>
                    <v-card-text>
                      <v-container>
                        <v-row>
                          <v-col>
                            <v-overflow-btn
                              v-model="proposal.status"
                              class="my-2"
                              :items="dropdown_font"
                              label="Status"
                              target="#dropdown-example"
                            ></v-overflow-btn>
                          </v-col>
                        </v-row>
                      </v-container>
                      <small></small>
                    </v-card-text>
                    <v-card-actions>
                      <v-spacer></v-spacer>
                      <v-btn
                        color="secondary"
                        text
                        @click="updateStatus(); statusDialogue = false; text = 'Status Updated'"
                      >Done</v-btn>
                    </v-card-actions>
                  </v-card>
                </v-dialog>
              </span>
            </p>
            <v-divider></v-divider>

            <v-list dense>
              <v-list-item>
                <v-list-item-content>Name:</v-list-item-content>
                <v-list-item-content class="align-end">{{ proposal.name }}</v-list-item-content>
              </v-list-item>
              <v-list-item>
                <v-list-item-content>Date of Birth:</v-list-item-content>
                <v-list-item-content class="align-end">{{ proposal.dob }}</v-list-item-content>
              </v-list-item>
              <v-list-item>
                <v-list-item-content>Postcode:</v-list-item-content>
                <v-list-item-content class="align-end">{{ proposal.postcode }}</v-list-item-content>
              </v-list-item>
              <v-list-item>
                <v-list-item-content>Mobile Number:</v-list-item-content>
                <v-list-item-content class="align-end">{{ proposal.mobile }}</v-list-item-content>
              </v-list-item>
              <v-list-item>
                <v-list-item-content>Email Address:</v-list-item-content>
                <v-list-item-content class="align-end">{{ proposal.email }}</v-list-item-content>
              </v-list-item>
            </v-list>
            <v-divider></v-divider>
          </v-card-text>
        </v-card>
      </div>
      <div class="col" style="padding-right: 50px; padding-top: 20px">
        <v-card class="mx-auto" max-width="344" min-width="100%" min-height="415">
          <v-card-text>
            <p class="display-3 text-center">
              <v-icon x-large>mdi-cash-multiple</v-icon>Quote Details
            </p>
            <p>
              <span class="title">
                Edit Quote
                <v-dialog v-model="dialog" persistent max-width="600px">
                  <template v-slot:activator="{ on }">
                    <v-btn class="mx-2" x-small fab dark color="secondary" v-on="on">
                      <v-icon dark>mdi-pencil</v-icon>
                    </v-btn>
                  </template>
                  <v-card>
                    <v-card-title>
                      <span class="headline">
                        <v-icon>mdi-cash-multiple</v-icon>Quote
                      </span>
                    </v-card-title>
                    <v-card-text>
                      <v-container>
                        <v-row>
                          <v-col cols="12" sm="6" md="4">
                            <v-text-field
                              v-model="proposal.advance"
                              label="Advance"
                              prefix="£"
                              color="secondary"
                              required
                              @keydown.enter="dialog = false; snackbar = true; text = 'Remember to recalculate!'"
                            ></v-text-field>
                          </v-col>
                          <v-col cols="12" sm="6" md="4">
                            <v-text-field
                              v-model="proposal.term"
                              label="Term"
                              suffix="Months"
                              color="secondary"
                              required
                              @keydown.enter="dialog = false; snackbar = true; text = 'Remember to recalculate!'"
                            ></v-text-field>
                          </v-col>
                          <v-col cols="12" sm="6" md="4">
                            <v-text-field
                              v-model="proposal.rate"
                              label="Rate"
                              suffix="%"
                              color="secondary"
                              persistent-hint
                              required
                              @keydown.enter="dialog = false; snackbar = true; text = 'Remember to recalculate!'"
                            ></v-text-field>
                          </v-col>
                        </v-row>
                      </v-container>
                      <small></small>
                    </v-card-text>
                    <v-card-actions>
                      <v-spacer></v-spacer>
                      <v-btn
                        color="secondary"
                        text
                        @click="dialog = false; snackbar = true; text = 'Remember to recalculate!'"
                      >Done</v-btn>
                    </v-card-actions>
                  </v-card>
                </v-dialog>Recalculate
                <v-btn class="mx-2" x-small fab dark color="secondary" @click="calculateQuote()">
                  <v-icon dark>mdi-calculator</v-icon>
                </v-btn>
              </span>
            </p>
            <v-divider></v-divider>
            <v-list dense>
              <v-list-item>
                <v-list-item-content>Advance:</v-list-item-content>
                <v-list-item-content class="align-end">£{{ proposal.advance }}</v-list-item-content>
              </v-list-item>
              <v-list-item>
                <v-list-item-content>Term:</v-list-item-content>
                <v-list-item-content class="align-end">{{ proposal.term }} Months</v-list-item-content>
              </v-list-item>
              <v-list-item>
                <v-list-item-content>Rate:</v-list-item-content>
                <v-list-item-content class="align-end">{{ proposal.rate }}%</v-list-item-content>
              </v-list-item>
              <v-list-item>
                <v-list-item-content>APR:</v-list-item-content>
                <v-list-item-content class="align-end">{{ proposal.apr }}%</v-list-item-content>
              </v-list-item>
              <v-list-item>
                <v-list-item-content>Payment:</v-list-item-content>
                <v-list-item-content class="align-end">£{{ proposal.payment }}</v-list-item-content>
              </v-list-item>
            </v-list>
            <v-divider></v-divider>
          </v-card-text>
        </v-card>
      </div>
    </div>

    <v-snackbar v-model="snackbar">
      {{ text }}
      <v-btn color="secondary" text @click="snackbar = false">Close</v-btn>
    </v-snackbar>
  </v-card>
</template>

<script>
import { eventBus } from "../main";
// import
export default {
  data: function() {
    return {
      dropdown_font: [
        "Awaiting Decision",
        "Approved",
        "Declined (Credit)",
        "Waiting For Documents",
        "Documents Sent for Signature"
      ],
      date: new Date().toISOString().substr(0, 10),
      menu: false,
      modal: false,
      menu2: false,
      loadingBool: false,
      urlStem: "http://publicduck.online:5050/api",
      dialog: false,
      statusDialogue: false,
      alignments: ["center"],
      snackbar: false,
      text: "Remember to recalculate!",
      viewButton: false,
      activeProposalNow: "",
      proposal: {},
      quote: {},
      response: "",
      request: {},
      notesDialogue: false,
      notes: {},
      noteBody: "",
      notePost: false,
      ruleShow: false,
      rules: {}
    };
  },
  methods: {
    getData: async function() {
      this.loadingBool = true;
      console.log(this.activeProposal);
      let fetchUrl =
        this.urlStem + "/proposal?proposalid=" + this.activeProposal;
      let response = await fetch(fetchUrl, {
        cors: true
      });
      let data = await response.json();

      console.log(data.proposal[0]);

      this.proposal = data.proposal[0];
      this.loadingBool = false;

      eventBus.$emit("dataPulled", this.viewButton);
      return data;
    },
    calculateQuote: async function() {
      this.loadingBool = true;
      this.request = {
        advance: this.proposal.advance,
        term: this.proposal.term,
        rate: this.proposal.rate
      };

      let fetchUrl =
        this.urlStem + "/proposal/" + this.activeProposal + "/quote";

      let response = await fetch(fetchUrl, {
        method: "PATCH",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json"
        },
        body: JSON.stringify(this.request),
        cors: true
      });

      let data = await response.json();

      console.log(data.quote);

      await setTimeout(() => {
        this.quote = data.quote;
        this.loadingBool = false;
        this.proposal.apr = data.quote.apr;
        this.proposal.payment = data.quote.payment;
        this.proposal.advance = data.quote.advance;
        this.proposal.term = data.quote.term;
        this.proposal.rate = data.quote.rate;
      }, 1000);

      return data;
    },
    updateStatus: async function() {
      this.request = {
        update: this.proposal.status
      };

      this.loadingBool = true;

      let fetchUrl =
        this.urlStem + "/proposal/" + this.activeProposal + "/status";

      let response = await fetch(fetchUrl, {
        method: "PATCH",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json"
        },
        body: JSON.stringify(this.request),
        cors: true
      });

      let data = await response.json();

      await setTimeout(() => {
        if (data.action == "n/a") {
          this.text = "Failed to Update";
          this.snackbar = true;
        } else {
          this.loadingBool = false;
          this.snackbar = true;
        }
      }, 1000);
    },
    loadNotes: async function() {
      let fetchUrl = this.urlStem + "/notes/" + this.activeProposal;
      let response = await fetch(fetchUrl);

      let data = await response.json();

      this.notes = data.notes.reverse();

      console.log(this.notes);
    },
    postNote: async function() {
      let fetchUrl = this.urlStem + "/notes/new";
      let body = {
        proposalId: this.activeProposal,
        noteBody: this.noteBody
      };

      let response = await fetch(fetchUrl, {
        method: "POST",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json"
        },
        body: JSON.stringify(body)
      });

      let data = await response.json();

      console.log(data);

      this.snackbar = true;
      this.text = "Note added";
    },
    loadRules: async function() {
      this.loadingBool = true;

      let fetchUrl = this.urlStem + "/rules?prop=" + this.activeProposal;
      let response = await fetch(fetchUrl, {
        cors: true
      });

      let data = await response.json()
      console.log(data)

      this.rules = data;
      this.loadingBool = false;
    }
  },
  created() {
    this.$emit("titleChanged", "Proposal");
    this.getData();
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