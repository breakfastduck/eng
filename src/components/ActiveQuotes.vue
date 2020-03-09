<template>
  <v-card style="min-height: 500px" class="mx-auto">
    <div style="min-height: 95%" class="row">
      <div class="col">
        <v-text-field v-model="activeProposalNow" color="amber darken-2" class="infield" label="Search by QuoteID" @keydown.enter="setActiveProposal()"></v-text-field>
      </div>
    </div>
    <div class="row">
      <div class="col">
        <div style="margin-left: 26px; margin-right: 26px;">
          <v-text-field
            color="amber"
            v-model="searchStatus"
            label="Filter by status"
            single-line
            hide-details
          ></v-text-field>
          <br />

          <v-progress-linear indeterminate color="yellow darken-2" :active="loadingBool"></v-progress-linear>
          <v-data-table
            color="amber"
            item-color="amber"
            :headers="headers"
            :items="activeQuotes"
            :items-per-page="5"
            class="elevation-1"
            :search="searchStatus"
            @click:row="rowClick"
            item-key="proposalId"
            single-select
          ></v-data-table>
        </div>
      </div>
    </div>
  </v-card>
</template>

<script>
import { eventBus } from "../main";

export default {
  data: function() {
    return {
      activeProposalNow: '',
      activeComponentNow: 'myProposal',
      searchStatus: '',
      searchTypes: ["ProposalID", "Name", "Postcode"],
      searchType: "ProposalID",
      searchValue: "",
      headers: [
        {
          text: "ProposalID",
          align: "start",
          sortable: false,
          value: "proposalId",
          filterable: false
        },
        { text: "Name", value: "name", filterable: false },
        { text: "Advance", value: "advance", filterable: false },
        { text: "Status", value: "status" }
      ],
      activeQuotes: [],
      loadingBool: true,
      urlStem: 'http://publicduck.online:5050/api'
    };
  },
  methods: {
    getCustomerData: async function () {
        let fetchUrl = this.urlStem + '/proposals?brokerid=' + this.brokerId
        let response = await fetch(fetchUrl, {
          cors: true
        })
        let data = await response.json()

        console.log(data.proposals)
                
        await setTimeout(() => {
          this.activeQuotes = data.proposals
          this.loadingBool = false
        }, 500);

                return data
    },
    logBroker() {
      console.log(this.brokerId)
    },
    setActiveProposal: function () {
      eventBus.$emit('proposalSelected', this.activeProposalNow)
      eventBus.$emit('activeState', this.activeComponentNow)


    },
    rowClick: function (item, row) {
                    row.select(true);
                    console.log(item.proposalId)
                    eventBus.$emit('proposalSelected', item.proposalId)
                    eventBus.$emit('activeState', this.activeComponentNow)
                    //item  - selected item
    }
  },
  created() {
    
    this.$emit("titleChanged", "Active Proposals");
    this.getCustomerData();

    console.log("quotes");
  },
  deactivated() {
    console.log("deactivated");
  },
  props: ["pgTitle", "brokerId"]
}
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