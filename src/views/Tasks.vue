<template>
  <v-list
    class="my-6"
    flat
    three-line
  >
    <v-list-item
      v-for="(task, index) in tasks"
      :key="task.id"
      class="pb-2"
    >
      <template>
        <v-list-item-action>
          <v-checkbox :input-value="task.completed" @click="toggleComplete(task.id)"></v-checkbox>
        </v-list-item-action>
        <v-list-item-content>
          <v-row>
            <v-col cols="9" md="5" sm="6">
              <v-list-item-title class="pb-2"> {{ task.name }} </v-list-item-title>
              <v-list-item-subtitle> {{ task.description }} </v-list-item-subtitle>
            </v-col>
            <v-col md="4" class="hidden-sm-and-down">
              <v-list-item-title class="pb-2"> Assigned to: </v-list-item-title>
              <v-list-item-subtitle v-if="task.username != null"> {{ task.username }} </v-list-item-subtitle>
              <v-list-item-subtitle v-else> - </v-list-item-subtitle>
            </v-col>
            <v-col md="2" sm="4" class="hidden-xs-only">
              <div class="d-flex">
                <v-icon
                  small
                  class="pr-2 pb-2"
                >
                  mdi-tag
                </v-icon>
              <v-list-item-title v-if="task.tag != null" class="pb-2"> {{ task.tag }} </v-list-item-title>
              <v-list-item-title v-else class="pb-2"> - </v-list-item-title>
              </div>
              <div class="d-flex">
                <v-icon
                  small
                  class="pr-2"
                >
                  mdi-calendar
                </v-icon>
                <v-list-item-subtitle v-if="task.due_date != null"> {{ task.formattedDate }} </v-list-item-subtitle>
                <v-list-item-subtitle v-else> - </v-list-item-subtitle>
              </div>
            </v-col>
            <v-col cols="1">
              <v-menu
                offset-y 
                transition="slide-y-transition"
              >
                <template v-slot:activator="{ on, attrs }">
                  <v-btn
                    icon
                    right
                    v-bind="attrs"
                    v-on="on"
                  >
                    <v-icon>
                      mdi-dots-vertical
                    </v-icon>
                  </v-btn>
                </template>
                <v-list>
                  <v-list-item
                    link
                    @click="showDialog(task)"
                  >
                    <v-icon class="mr-3"> mdi-pencil </v-icon>
                    <v-list-item-title> Edit </v-list-item-title>
                  </v-list-item>
                  <v-list-item
                    link
                    @click="deleteTask(task, index)"
                  >
                    <v-icon class="mr-3"> mdi-delete </v-icon>
                    <v-list-item-title> Delete </v-list-item-title>
                  </v-list-item>
                </v-list>
              </v-menu>
            </v-col>
          </v-row>
        </v-list-item-content>
      </template>
    </v-list-item>
    <AddTask @add-task="addTask"></AddTask>
    <EditTask @edit-task="editTask" ref="editTask"></EditTask>
  </v-list>
</template>

<script>
import axios from 'axios'
import AddTask from '../components/AddTask.vue'
import EditTask from '../components/EditTask.vue'

export default {
  data () {
    return {
      tasks: []
    }
  },
  components: {
    AddTask,
    EditTask
  },
  methods: {
    // Method that makes an API call that returns all tasks.
    getTasks() {
      axios.get('http://127.0.0.1:8000/api/tasks')
        .then((response) => {
          this.tasks = response.data
          this.tasks.forEach(task => {
            // Get the name of our users
            if (task.user_id != null) {
              let username = this.$store.state.users.find(user => user.id == task.user_id)
              task.username = username.name
            }
            // Format the date
            if (task.due_date != null) {
              task.formattedDate = task.due_date
              task.formattedDate = task.due_date.substr(8,2) + '/' + task.due_date.substr(5,2) + '/' + task.due_date.substr(0,4)
            }
          })
        })
        .catch(function (error) {
	        console.log(error.response.data)
      })
    },
    // Method that executes a method on child component "EditTask"
    showDialog(task) {
      this.$refs.editTask.showDialog(task)
    },
    // Method that makes an API call that deletes a task.
    deleteTask(task, index) {
      axios.delete('http://127.0.0.1:8000/api/task/' + task.id)
        .then((response) => {
          // Delete the task from local array
          this.tasks.splice(index, 1)
        })
        .catch(function (error) {
	        console.log(error.response.data)
      })
    },
    // Method that makes an API call to quickly update completion status of a task.
    toggleComplete(id) {
      let localTask = this.tasks.filter(tasks => tasks.id === id)
      localTask.completed = !localTask.completed
      axios.put('http://127.0.0.1:8000/api/task/status/' + id)
        .catch(function (error) {
	        console.log(error.response.data)
      })
      console.log(this.tasks)
    },
    // Method that adds a new task to beginning of the tasks array.
    addTask (newTask) {
      // Assigned_to
      if (newTask.user_id != null) {
        let username = this.$store.state.users.find(user => user.id == newTask.user_id)
        newTask.username = username.name
      }
      // Date format DD/MM/YYYY
      if (newTask.due_date != null)
        newTask.formattedDate = newTask.due_date.substr(8,2) + '/' + newTask.due_date.substr(5,2) + '/' + newTask.due_date.substr(0,4)
      
      this.tasks.splice(0,0,newTask)
    },
    // Method that edits local task array to update an edited task
    editTask (editedTask) {
      let index = this.tasks.findIndex(task => task.id === editedTask.id)
      this.tasks[index].name = editedTask.name
      this.tasks[index].tag = editedTask.tag
      this.tasks[index].assigned_to = editedTask.assigned_to
      this.tasks[index].description = editedTask.description
      this.tasks[index].due_date = editedTask.due_date
      this.tasks[index].formattedDate = editedTask.formattedDate
    }
  },
  created () {
    this.getTasks()
  }
}
</script>
