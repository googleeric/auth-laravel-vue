<template>
    <v-app id="inspire">
    <v-navigation-drawer
      v-model="drawer"
      app
      clipped
    >
      <v-list dense>
        <v-list-item
          v-for="item in items"
          :key="item.text"
          link
          :to="item.action"
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>
              {{ item.text }}
            </v-list-item-title>
          </v-list-item-content>
        </v-list-item>
        <v-subheader class="mt-4 grey--text text--darken-1">SUBSCRIPTIONS</v-subheader>
        <v-list>
          <v-list-item
            v-for="item in items2"
            :key="item.text"
            link
          >
            <v-list-item-avatar>
              <img
                :src="`https://randomuser.me/api/portraits/men/${item.picture}.jpg`"
                alt=""
              >
            </v-list-item-avatar>
            <v-list-item-title v-text="item.text"></v-list-item-title>
          </v-list-item>
        </v-list>
        <v-list-item
          class="mt-4"
          link
        >
          
          <v-list-item-title class="grey--text text--darken-1"><v-switch v-model="theme" class="ma-4" label="Switch Theme"></v-switch></v-list-item-title>
        </v-list-item>
        <v-list-item link>
          <v-list-item-action>
            <v-icon color="grey darken-1">mdi-cog</v-icon>
          </v-list-item-action>
          <v-list-item-title class="grey--text text--darken-1">Manage Subscriptions</v-list-item-title>
        </v-list-item>
        <v-list-item link>
          <v-list-item-action>
            <v-icon color="grey darken-1">mdi-account</v-icon>
          </v-list-item-action>
          <v-list-item-title class="grey--text text--darken-1" @click="logout">Logout</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-app-bar
      app
      clipped-left
      color="red"
      dense
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
      <v-btn text to="/admin" left>
      <v-icon
        class="mx-4"
        large
      >
        mdi-laravel
      </v-icon>
      <v-toolbar-title class="mr-12 align-center">
        
        <span class="title">Laravue Admin</span>
      </v-toolbar-title>
      </v-btn>
      <v-spacer></v-spacer>
      <v-row
        align="center"
        style="max-width: 650px"
      >
        <v-text-field
          :append-icon-cb="() => {}"
          placeholder="Search..."
          single-line
          append-icon="mdi-magnify"
          color="white"
          hide-details
        ></v-text-field>
      </v-row>
    </v-app-bar>

    <v-main>
      <v-container class="">
            <router-view></router-view>
        <v-row
          justify="center"
          align="center"
        >
          <v-col>
            <v-snackbar
            v-model="snackbar"
          >
            You Are Logged In Successfully

            <template v-slot:action="{ attrs }">
              <v-btn
                color="pink"
                text
                v-bind="attrs"
                @click="snackbar = false"
              >
                Close
              </v-btn>
            </template>
            </v-snackbar>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
    props: {
      source: String
    },
    data: () => ({
      drawer: null,
      snackbar:false,
      theme:true,
      items: [
        { icon: 'mdi-trending-up', text: 'Users', action: 'users' },
        { icon: 'mdi-youtube-subscription', text: 'Posts', action: 'posts' },
        { icon: 'mdi-history', text: 'Pages', action: 'pages' },
        { icon: 'mdi-playlist-play', text: 'Categories', action: 'categories' },
        { icon: 'mdi-clock', text: 'Roles', action: '/admin/roles' },
      ],
      items2: [
        { picture: 28, text: 'Joseph' },
        { picture: 38, text: 'Apple' },
        { picture: 48, text: 'Xbox Ahoy' },
        { picture: 58, text: 'Nokia' },
        { picture: 78, text: 'MKBHD' },
      ],
    }),
    created () {
      this.$vuetify.theme.dark = true
    },
    watch:{
      theme: function(old, newval){
        this.$vuetify.theme.dark = old
      }   
    },
    mounted(){
      this.snackbar = localStorage.getItem('loggenIn', true);
      localStorage.removeItem('loggenIn');
    },
    methods:{
      logout: function(){
        localStorage.removeItem('token')
        this.$router.push('/login')
        .then(res => {
          localStorage.setItem('loggedOut', true);
        })
        .catch(err => console.log(err))
      }
    }
  }
</script>

<style lang="stylus" scoped>

</style>