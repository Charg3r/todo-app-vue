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
                    :items="['Carlos Olivas', 'John Doe', 'Will Smith', 'Luis Miguel']"
                    prepend-icon="mdi-account"
                    v-model="editedTask.assigned_to"
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
            Edit
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<script>
import axios from 'axios'

export default {
  computed: {
    users: {
      get() {
        return this.$store.state.users
      }
    }
  },
  data () {
    return {
        dialog: false,
        menu: false,
        valid: true,
        editedTask: [],
        nameRules: [
          v => !!v || 'Name is required'
        ],
        descriptionRules: [
          v => /.+@.+\..+/.test(v) || 'Description must be less than 500 characters',
        ]
    }
  },
  methods: {
    showDialog(task) {
      this.editedTask = JSON.parse(JSON.stringify(task));
      this.dialog = true
    },
    editTask() {
      axios.put('http://127.0.0.1:8000/api/task/' + this.editedTask.id, {
        task:{
          name: this.editedTask.name,
          description: this.editedTask.description,
          tag: this.editedTask.tag,
          due_date: this.editedTask.due_date
        }
      }).then((response) => {
        console.log(response)
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