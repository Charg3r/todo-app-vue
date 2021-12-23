<template>
  <v-row justify="center">
    <v-dialog
      v-model="dialog"
      max-width="600px"
    >
      <template v-slot:activator="{ on, attrs }">
        <v-btn
          class="add-task"
          color="indigo"
          dark
          fab
          v-bind="attrs"
          v-on="on"
        >
          <v-icon dark>
            mdi-plus 
          </v-icon>
        </v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="text-h5">New task</span>
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
                    v-model="newTask.name"
                    prepend-icon="mdi-check-circle"
                    required
                  ></v-text-field>
                </v-col>
                <v-col
                  cols="12"
                  sm="6"
                >
                  <v-text-field
                    v-model="newTask.tag"
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
                    :return-value.sync="newTask.due_date"
                    transition="scale-transition"
                    offset-y
                    min-width="auto"
                  >
                    <template v-slot:activator="{ on, attrs }">
                      <v-text-field
                          v-model="newTask.due_date"
                          label="Due date"
                          prepend-icon="mdi-calendar"
                          readonly
                          v-bind="attrs"
                          v-on="on"
                      ></v-text-field>
                    </template>
                    <v-date-picker
                      v-model="newTask.due_date"
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
                          @click="$refs.menu.save(newTask.due_date)"
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
                    :items="['Carlos Olivas', 'John Doe', 'Will Smith', 'Luis Miguel']"
                    prepend-icon="mdi-account"
                    v-model="newTask.assigned_to"
                    label="Assigned to"
                  ></v-select>
                </v-col>
                <v-col cols="12">
                  <v-text-field
                    label="Description"
                    v-model="newTask.description"
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
            @click="createTask"
          >
            Create
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
        nameRules: [
          v => !!v || 'Name is required'
        ],
        descriptionRules: [
          v => /.+@.+\..+/.test(v) || 'Description must be less than 500 characters',
        ],
        newTask: []
    }
  },
  methods: {
    createTask() {
      axios.post('http://127.0.0.1:8000/api/task/store', {
        task:{
          name: this.newTask.name,
          description: this.newTask.description,
          tag: this.newTask.tag,
          due_date: this.newTask.due_date
        }
      }).then((response) => {
        console.log(response)
        this.$emit('add-task', this.newTask)
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
  .add-task {
    position: absolute;
    bottom: 5%;
    right: 5%;
  }
</style>