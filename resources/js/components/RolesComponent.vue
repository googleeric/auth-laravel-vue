<template>
  <v-data-table 
  item-key="name" 
  class="elevation-1" 
  :loading = "loading"
  loading-text="Loading... Please wait"
  :headers="headers"
  :search="search"
  @pagination='paginate'
    :items="roles"
    :items-per-page=5
    sort-by="calories"
    :footer-props="{
      itemsPerPageText: 'Roles Per Page',
      'show-current-page': true,
      'show-first-last-page':true

      }"
  >
      <template v-slot:top>
      <v-toolbar flat color="dark">
        <v-toolbar-title>Role Management System</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        
        <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
      ></v-text-field>
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              color="error"
              dark
              class="mb-2"
              v-bind="attrs"
              v-on="on"
            >New Item</v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="headline">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" sm="12">
                    <v-text-field v-model="editedItem.name" label="Role Name"></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="error darken-1" text @click="close">Cancel</v-btn>
              <v-btn color="error darken-1" text @click="save">Save</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <v-icon
        small
        class="mr-2"
        @click="editItem(item)"
      >
        mdi-pencil
      </v-icon>
      <v-icon
        small
        @click="deleteItem(item)"
      >
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn color="error" @click="initialize">Reset</v-btn>
    </template>
    <v-snackbar
            v-model="snackbar"
          >
            Record Deleted Successfully

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
  </v-data-table>
</template>
<script>
  export default {
    data: () => ({
      dialog: false,
      loading: false,
      snackbar:false,
      search: '',
      headers: [
        {
          text: '#',
          align: 'start',
          sortable: false,
          value: 'id',
        },
        { text: 'Name', value: 'name' },
        { text: 'Created At', value: 'created_at' },
        { text: 'Updated At', value: 'updated_at' },
        { text: 'Actions', value: 'actions', sortable: false },
      ],
      roles: [],
      editedIndex: -1,
      editedItem: {
        id: '',
        name: '',
        created_at: '',
        updated_at: ''
      },
      defaultItem: {
        id: '',
        name: '',
        created_at: '',
        updated_at: ''
      },
    }),

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
      },
    },

    watch: {
      dialog (val) {
        val || this.close()
      },
    },

    created () {
      this.initialize()
    },

    methods: {
      paginate($event){
            axios.get('/api/roles',{})
            .then(res => this.roles = res.data.roles )
            .catch(err => {
              if(err.response.status == 401)
              localStorage.removeItem('token');
              this.$router.push('/login')
            } )
      },
      initialize () {
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
      },

      editItem (item) {
        this.editedIndex = this.roles.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },

      deleteItem (item) {
        const index = this.roles.indexOf(item)
        let decide = confirm('Are you sure you want to delete this item?')
        if(decide){
          axios.delete('/api/roles/'+item.id)
          .then(res => {
            this.snackbar=true
            this.roles.splice(index, 1)
          })
          .catch(err => console.log(err.response))
        }
       
      },

      close () {
         this.dialog = false
        setTimeout(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
        //  this.editedIndex = -1
        }, 300)
      },

      save () {
        if (this.editedIndex > -1) {
          axios.put('/api/roles/'+this.editedItem.id, {
            'name': this.editedItem.name
          })
          .then(res => Object.assign(this.roles[this.editedIndex], res.data.role))
          .catch(err => console.log(err.response))
          
        } else {
          axios.post('/api/roles',{
            'name' : this.editedItem.name
          })
          .then(res=> this.roles.push(res.data.role))
          .catch(err => console.dir(err.response))
        }
        this.close()
      },
    },
  }
</script>
<style lang="stylus" scoped></style>