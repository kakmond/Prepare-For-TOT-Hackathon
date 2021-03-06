<template>
  <div>
    <v-navigation-drawer temporary v-model="sideNav" app>
      <v-toolbar v-if="isAuthenticated" flat class="transparent">
        <v-list class="pa-0">
          <v-list-tile @click="redirect('/profile')" avatar>
            <v-list-tile-avatar>
              <img :src="`${this.getUser.photoURL}`" alt="userImg">
            </v-list-tile-avatar>

            <v-list-tile-content>
              <v-list-tile-title>{{this.getUser.displayName.split(" ")[0]}}</v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
        </v-list>
      </v-toolbar>

      <v-list class="pt-0" dense>
        <v-divider></v-divider>
        <v-list-tile @click="redirect('/')">
          <v-list-tile-action>
            <v-icon>home</v-icon>
          </v-list-tile-action>

          <v-list-tile-content>
            <v-list-tile-title>Home</v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>

        <v-list-tile @click="redirect('/Weather')">
          <v-list-tile-action>
            <v-icon>supervisor_account</v-icon>
          </v-list-tile-action>

          <v-list-tile-content>
            <v-list-tile-title>Weather</v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer>

    <v-toolbar app dark class="warning">
      <v-toolbar-side-icon @click.stop="sideNav = !sideNav" class="hidden-sm-and-up"></v-toolbar-side-icon>
      <!-- Toolbar label -->
      <v-toolbar-title>TOT Weather Care</v-toolbar-title>
      <!-- Branch selection in nav bar-->
      <v-flex class="hidden-md-and-down" :style="branchSelectionStyle">
        <v-select v-model="select" :items="items" label="Select your Branch"></v-select>
      </v-flex>
      <!-- Option -->
      <v-toolbar-items class="hidden-xs-only">
        <v-btn to="/" class="subheading" flat>
          <v-icon left dark>home</v-icon>Home
        </v-btn>
        <!-- <v-btn to="/Weather" class="subheading" flat>
          <v-icon left dark>supervisor_account</v-icon>Weather
        </v-btn>-->
        
        <v-bottom-sheet class="pt-3" v-model="sheet">
          <v-btn flat slot="activator">
            <v-badge color="red">
              <span slot="badge">!</span>
              <v-icon large color="white">mail</v-icon>
            </v-badge>
          </v-btn>
          <v-list>
            <v-subheader>Notification</v-subheader>
            <v-list-tile  @click="sheet = false">
              <v-list-tile-avatar>
                <v-avatar size="32px" tile>
                  <img
                    :src="`https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT-Z3SNHwfuNED0Qv8sTzGRkOZGTLmh2F2fr6puy9p7uqoWQGQx`"
                  >
                </v-avatar>
              </v-list-tile-avatar>
              <v-list-tile-title>Today is good day</v-list-tile-title>
            </v-list-tile>
          </v-list>
        </v-bottom-sheet>

        <v-btn class="subheading" v-if="!isAuthenticated" flat @click="openDialog">Get Started
          <v-icon right dark>navigate_next</v-icon>
        </v-btn>

        <v-layout v-else>
          <v-btn to="/Profile" flat class="body-2" @click="profileInfo">
            <v-avatar class>
              <img :src="`${this.getUser.photoURL}`" alt="userImg">
            </v-avatar>
            <span class="pl-2">{{this.getUser.displayName.split(" ")[0]}}</span>
          </v-btn>
          <v-btn @click="signOut" flat>Sign Out
            <v-icon left dark>keyboard_arrow_right</v-icon>
          </v-btn>
        </v-layout>
      </v-toolbar-items>
    </v-toolbar>
    <!-- Branch selection below -->
    <v-flex class="hidden-lg-and-up" style="margin-top: 50px" :style="branchSelectionStyle">
      <v-select v-model="select" :items="items" label="Select your Branch"></v-select>
    </v-flex>
    <!-- Login dialog -->
    <v-dialog v-model="dialogVisible" max-width="600">
      <v-card>
        <v-card-title style="font-size: 25px">Welcome Back!</v-card-title>
        <v-divider></v-divider>

        <v-card-text
          style="font-size: 20px; color: #a6a6a6"
        >Sign in to access your personalized homepage, follow authors and topics you love, and clap for stories that matter to you.</v-card-text>

        <v-card-actions>
          <v-layout row wrap>
            <v-flex lg6 sm12>
              <SignInFacebookBtn/>
            </v-flex>
            <v-flex lg6 sm12>
              <SignInGoogleBtn/>
            </v-flex>
          </v-layout>
        </v-card-actions>
        <v-card-text
          style="font-size: 15px; color: #a6a6a6"
        >Click “Sign in” above to accept Medium’s Terms of Service and Privacy Policy.</v-card-text>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import { LOGOUT } from "@/store/actions.type";
import SignInFacebookBtn from "@/components/auth/SignInFacebookBtn";
import SignInGoogleBtn from "@/components/auth/SignInGoogleBtn";
import { FETCH_ARTICLE, GETPROFILE } from "@/store/actions.type";

export default {
  name: "Header",
  components: { SignInFacebookBtn, SignInGoogleBtn },
  data() {
    return {
      select: "Bangsaothong",
      activeIndex: this.$route.path,
      dialogVisible: false,
      loading: true,
      sideNav: false,
      sheet: false,
      items: ["Bangsaothong", "Bangpeeyai", "Bangbor"]
    };
  },
  methods: {
    openDialog() {
      this.dialogVisible = true;
    },
    signOut() {
      this.$store.dispatch(LOGOUT);
    },
    redirect(url) {
      window.location.href = url;
    },
    changeBranch(branch) {
      console.log(this.selectedItem);
    }
  },
  computed: {
    ...mapGetters(["getUser", "isAuthenticated", "isLoading"]),
    branchSelectionStyle() {
      return {
        padding: "10px 100px 0px 100px"
      };
    }
  },
  watch: {
    isAuthenticated(value) {
      if (value) {
        this.dialogVisible = false;
      }
    },
    select(value) {
      console.log(value);
      this.$store.dispatch(FETCH_ARTICLE, value);
    }
  },
  mounted: function() {
    this.$store.dispatch(FETCH_ARTICLE, this.select);
  }
};
</script>

<style scoped>
@media (max-width: 600px) {
}
</style>