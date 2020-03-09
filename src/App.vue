<template>
  <v-app class="bgImage" style="background-color: #363636;">
    <v-app-bar app color="primary" dark flat>
      <v-app-bar-nav-icon color="secondary" @click="drawer = true"></v-app-bar-nav-icon>
      <v-toolbar-title style="color: secondary">Engager</v-toolbar-title>
      <v-divider class="mx-4" inset vertical></v-divider>
      <transition name="component-fade" mode="out-in">
        <span class="subheading">{{ pgTitle }}</span>
      </transition>

      <v-spacer></v-spacer>
      <span v-if="activeProposal" class="subheading">
        <v-btn icon x-small color="secondary" @click="activeComponent = 'myProposal'">
          <v-icon>mdi-file-find</v-icon>
        </v-btn>
        Active Proposal: {{ activeProposal }}
      </span>
      <v-divider v-if="activeProposal" class="mx-4" inset vertical></v-divider>
      <span class="subheading">{{ brokerId }}</span>
      <v-divider class="mx-4" inset vertical></v-divider>
      <v-btn href="https://github.com/vuetifyjs/vuetify/releases/latest" target="_blank" text>
        <span class="mr-2">api docs</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>
    <v-navigation-drawer v-model="drawer" width="199" absolute temporary color="#e19101">
      <v-list nav dense>
        <v-list-item-group v-model="group" active-class="white--text text--accent-4">
          <v-list-item @click="activeComponent = 'myHome'; drawer = false">
            <v-list-item-icon>
              <v-icon class="white--text">mdi-home</v-icon>
            </v-list-item-icon>
            <v-list-item-title class="white--text">Home</v-list-item-title>
          </v-list-item>
          <v-list-item @click="activeComponent = 'myActiveQuotes'; drawer = false">
            <v-list-item-icon>
              <v-icon class="white--text">mdi-account-group</v-icon>
            </v-list-item-icon>
            <v-list-item-title class="white--text">Active Proposals</v-list-item-title>
          </v-list-item>
          <v-list-item @click="activeComponent = 'myNewQuote'; drawer = false">
            <v-list-item-icon>
              <v-icon class="white--text">mdi-account-edit</v-icon>
            </v-list-item-icon>
            <v-list-item-title class="white--text">New Proposal</v-list-item-title>
          </v-list-item>
          <v-list-item>
            <v-list-item-icon>
              <v-icon class="white--text">mdi-file-chart</v-icon>
            </v-list-item-icon>
            <v-list-item-title class="white--text">Reports</v-list-item-title>
          </v-list-item>
          <v-list-item>
            <v-list-item-icon>
              <v-icon class="white--text">mdi-cube-scan</v-icon>
            </v-list-item-icon>
            <v-list-item-title class="white--text">Account</v-list-item-title>
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </v-navigation-drawer>
    <v-content>
      <v-container
        style="background-color: #363636; max-width: 98%; min-height: 100%"
        class="elevation-9 bgImage"
      >
        <transition name="component-fade" mode="out-in">
          <component
            :brokerId="brokerId"
            :activeProposal="activeProposal"
            style="max-width: 99%"
            :pgTitle="pgTitle"
            @titleChanged="pgTitle = $event"
            :is="activeComponent"
          ></component>
        </transition>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import Home from "./components/Home.vue";
import ActiveQuotes from "./components/ActiveQuotes.vue";
import NewQuote from "./components/NewQuote.vue";
import Proposal from "./components/ActiveProposal.vue"
import { eventBus } from "./main";

export default {
  name: "App",
  data: () => ({
    drawer: false,
    group: "",
    activeComponent: "myHome",
    pgTitle: "Wallasr",
    brokerId: "",
    activeProposal: ""
  }),
  methods: {
    test() {
      console.log("triggerd");
    }
  },
  components: {
    myHome: Home,
    myActiveQuotes: ActiveQuotes,
    myNewQuote: NewQuote,
    myProposal: Proposal
  },
  created() {
    eventBus.$on("brokerIdUpdated", data => {
      this.brokerId = data;
    });
    eventBus.$on("proposalSelected", data => {
      this.activeProposal = data;
    });
    eventBus.$on("activeState", data => {
      this.activeComponent = data;
    });
  }
};
</script>

<style>
.component-fade-enter-active,
.component-fade-leave-active {
  transition: opacity 0.2s ease;
}
.component-fade-enter, .component-fade-leave-to
/* .component-fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
.bgImage {
  background-image: url(https://images.pexels.com/photos/949587/pexels-photo-949587.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260);
  background-size: cover;
}
</style>