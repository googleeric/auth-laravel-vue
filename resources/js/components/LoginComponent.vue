<template>
<v-app id="inspire">
    <v-main>
      <v-container
        class="fill-height"
        fluid
      >
        <v-row
          align="center"
          justify="center"
        >
          <v-col
            cols="12"
            sm="8"
            md="4"
          >
            <v-card class="elevation-12">
              <v-toolbar
                color="error"
                dark
                flat
              >
                <v-toolbar-title>Login form</v-toolbar-title>
                
              </v-toolbar>
              <v-card-text>
                <v-progress-linear
                :active="loading"
                :indeterminate="loading"
                absolute
                top
                color="deep-purple accent-4"
              ></v-progress-linear>
                <v-form
                ref="form"
                v-model="valid"
                >  
                  <v-text-field
                    label="Login"
                    name="email"
                    :rules="emailRules"
                    v-model="email"
                    prepend-icon="mdi-account"
                    type="email"
                  ></v-text-field>

                  <v-text-field
                    id="password"
                    label="Password"
                    name="password"
                    :rules="passwordRules"
                    v-model="password"
                    prepend-icon="mdi-lock"
                    type="password"
                  ></v-text-field>
                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="error" :disabled="!valid" @click="login">Login</v-btn>
              </v-card-actions>
            </v-card>
            <v-snackbar
            v-model="snackbar"
          >
            {{ snacktext }}

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
          <v-snackbar
            v-model="loggedoutsnackbar"
          >
            Logged Out Successfully

            <template v-slot:action="{ attrs }">
              <v-btn
                color="pink"
                text
                v-bind="attrs"
                @click="loggedoutsnackbar = false"
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
    data(){
        return{
            valid: true,
            email: '',
            emailRules: [
              v => !!v || 'E-mail is required',
              v => /.+@.+\..+/.test(v) || 'E-mail must be valid',
            ],
            password: '',
            passwordRules: [
              v => !!v || 'Password is required'
            ],
            loading: false,
            snackbar: false,
            snacktext: '',
            loggedoutsnackbar: false
        }
    },
    created(){
      this.$vuetify.theme.dark = true;
    },
    mounted(){
      // this.snacktext = "Logged Out Successfully";
      this.loggedoutsnackbar = localStorage.getItem('loggedOut', true);
      localStorage.removeItem('loggedOut');
    },
    methods:{
        login: function(){
          // Add a request interceptor
          axios.interceptors.request.use((config) => {
              // Do something before request is sent
              this.loading = true;
              return config;
            }, function (error) {
              // Do something with request error
              this.loading = false;
              return Promise.reject(error);
            });
          // Add a response interceptor
          axios.interceptors.response.use((response) => {
              // Any status code that lie within the range of 2xx cause this function to trigger
              // Do something with response data
              this.loading = false;
              return response;
            }, (error) => {
              // Any status codes that falls outside the range of 2xx cause this function to trigger
              // Do something with response error
              this.loading = false;
              return Promise.reject(error);
            });
            axios.post('api/login',{
              'email' : this.email,
              'password': this.password
            })
            .then( res => {
              localStorage.setItem('token',res.data.token)
              localStorage.setItem('loggenIn', true)
              this.$router.push('/admin')
              .then(res => console.log('Logged in Successfully'))
              .catch(err => console.log(err))
            })
            .catch( err => {
              this.snacktext = err.response.data.status
              this.snackbar = true
            })
            
        }
    }
}
</script>
<style lang="stylus" scoped></style>