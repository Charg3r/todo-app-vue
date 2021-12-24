<template>
  <v-row justify="center">
    <v-dialog
      v-model="dialog"
      max-width="600px"
    >
      <v-card>
        <v-card-title>
          <span class="text-h5">Edit task</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-form
              ref="form"
              v-model="valid"
              lazy-validation
            >
              <v-row>
                <v-col
                  cols="12"
                  sm="6"
                >
                  <v-text-field
                    label="Name"
                    :rules="nameRules"
                    v-model="editedTask.name"
                    prepend-icon="mdi-check-circle"
                    required
                  ></v-text-field>
                </v-col>
                <v-col
                  cols="12"
                  sm="6"
                >
                  <v-text-field
                    v-model="editedTask.tag"
                    label="Tag"
                    prepend-icon="mdi-tag"
                  ></v-text-field>
                </v-col>
                <v-col
                  cols="12"
                  sm="6"
                >
                  <v-menu
                    ref="menu"
                    v-model="menu"
                    :close-on-content-click="false"
                    :return-value.sync="editedTask.due_date"
                    transition="scale-transition"
                    offset-y
                    min-width="auto"
                  >
                    <template v-slot:activator="{ on, attrs }">
                      <v-text-field
                        v-model="editedTask.due_date"
                        label="Due date"
                        prepend-icon="mdi-calendar"
                        readonly
                        v-bind="attrs"
                        v-on="on"
                      ></v-text-field>
                    </template>
                    <v-date-picker
                      v-model="editedTask.due_date"
                      no-title
                      scrollable
                      >
                      <v-spacer></v-spacer>
                      <v-btn
                          text
                          color="indigo"
                          @click="menu = false"
                      >
                          Cancel
                      </v-btn>
                      <v-btn
                          text
                          color="primary"
                          @click="$refs.menu.save(editedTask.due_date)"
                      >
                          OK
                      </v-btn>
                    </v-date-picker>
                  </v-menu>
                  </v-col>
                <v-col
                  cols="12"
                  sm="6"
                >
                  <v-select
                    :items="users"
                    item-value="id"
                    item-text="name"
                    prepend-icon="mdi-account"
                    v-model="editedTask.user_id"
                    label="Assigned to"
                  ></v-select>
                </v-col>
                <v-col cols="12">
                  <v-text-field
                    label="Description"
                    v-model="editedTask.description"
                    prepend-icon="mdi-clipboard-text"
                  ></v-text-field>
                </v-col>
              </v-row>
            </v-form>
          </v-container>
        </v-card-text>
        <v-card-actions>

          <v-spacer></v-spacer>

          <v-btn
            color="blue darken-1"
            text
            @click="dialog = false"
          >
            Close
          </v-btn>
          <v-btn
            color="blue darken-1"
            text
            @click="editTask"
          >
            Save
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<script>
  import axios from 'axios'

  export default {
    data () {
      return {
        dialog: false,
        menu: false,
        valid: true,
        editedTask: [],
        nameRules: [
          v => !!v || 'Name is required'
        ]
      }
    },
    computed: {
      users: {
        get() { // Get all stored users
          return this.$store.state.users
        }
      }
    },
    methods: {
      // Method that can be triggered from parent component
      showDialog(task) {
        this.editedTask = JSON.parse(JSON.stringify(task)); // Deep copy of task to be edited
        this.dialog = true
      },
      showError() {
         this.errorAlert = true
      },
      // Method that makes an API call to update the edited task
      editTask() {
        axios.put('http://127.0.0.1:8000/api/task/' + this.editedTask.id, {
          task:{
            name: this.editedTask.name,
            description: !this.editedTask.description ? "" : this.editedTask.description,
            tag: !this.editedTask.tag ? "" : this.editedTask.tag,
            user_id: !this.editedTask.user_id  ? "" : this.editedTask.user_id,
            due_date: !this.editedTask.due_date  ? "" : this.editedTask.due_date
          }
        }).then((response) => {
          this.editedTask = response.data
          console.log()
          this.$emit('edit-task', this.editedTask)
          this.dialog = false
        })
        .catch(function (error) {
          console.log(error.response.data)
        })
      }
    }
  }
</script>

<style>

</style>