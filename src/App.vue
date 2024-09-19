<template>
  <v-app>
    <v-container fluid>
  
      <v-row class="d-flex align-stretch">

        <v-col cols="12" md="4" class="d-flex flex-column">
          <v-card class="pa-4 flex-grow-1" color="secondary">
            <v-card-title class="headline">Daily Progress</v-card-title>
            <v-divider></v-divider>

            <div class="progress-container mb-4">
              <v-progress-linear
                :value="progress"
                height="25"
                class="progress-bar" :style="{ width: progress + '%' }"
              >
                <template v-slot:default>
                  <span class="progress-label">{{ Math.round(progress) }}%</span>
                </template>
              </v-progress-linear>
            </div>

            <v-card-title>Tasks</v-card-title>
            <v-divider></v-divider>
          
            <v-list class="bg">
              <v-list-item @click="selectCategory('Personal')">
                <v-list-item-content>
                  <v-list-item-title>Personal</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item @click="selectCategory('Work')">
                <v-list-item-content>
                  <v-list-item-title>Work</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item @click="selectCategory('Health')">
                <v-list-item-content>
                  <v-list-item-title>Health</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item @click="selectCategory('Social')">
                <v-list-item-content>
                  <v-list-item-title>Social</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list>
          </v-card>
        </v-col>

        <v-col cols="12" md="8" class="d-flex flex-column">
          <v-card class="pa-4 flex-grow-1" color="secondary">
            <v-card-title class="headline">Add To List</v-card-title>
            <v-divider></v-divider>
            <v-form>
              <v-text-field 
                v-model="newTaskTitle" 
                label="Task Title" 
                dense
                hide-details
              />
              <v-textarea 
                v-model="newTaskDetails" 
                label="Task Details" 
                dense
                hide-details
                rows="3"
              />
              <v-select 
                v-model="selectedCategory" 
                :items="categories" 
                label="Category" 
                dense
                hide-details
              />
              <v-btn @click="addTask" class="mt-5 ms-5 bg">Add to List</v-btn>
            </v-form>
          </v-card>
        </v-col>
      </v-row>

      <v-row>
        <v-col cols="12">
          <ToDoList 
            :tasks="tasks" 
            :selectedCategory="selectedCategory" 
            @edit-task="editTask" 
            @toggle-complete="toggleComplete" 
            @delete-task="deleteTask" 
          />
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
import ToDoList from './components/ToDoList.vue';

export default {
  components: {
    ToDoList
  },
  data() {
    return {
      newTaskTitle: '',
      newTaskDetails: '',
      selectedCategory: null,
      tasks: [],
      progress: 0,
      categories: ['Personal', 'Work', 'Health', 'Social'],
    };
  },
  methods: {
    selectCategory(category) {
      this.selectedCategory = category;
    },
    addTask() {
      if (this.newTaskTitle.trim() && this.newTaskDetails.trim() && this.selectedCategory) {
        this.tasks.push({
          id: Date.now(),
          title: this.newTaskTitle.trim(),
          details: this.newTaskDetails.trim().split('\n'),
          category: this.selectedCategory,
          completed: false,
        });
        this.newTaskTitle = '';
        this.newTaskDetails = '';
        this.saveTasksToLocalStorage();
        this.updateProgress();
      } else {
        console.log('Please fill all fields');
      }
    },
    editTask(task) {
      const newTitle = prompt('Edit task title:', task.title);
      const newDetails = prompt('Edit task details (separate by new lines):', task.details.join('\n'));
      if (newTitle !== null && newDetails !== null) {
        task.title = newTitle;
        task.details = newDetails.split('\n');
        this.saveTasksToLocalStorage();
      }
    },
    toggleComplete(task) {
      task.completed = !task.completed;
      this.saveTasksToLocalStorage();
      this.updateProgress();
    },
    deleteTask(task) {
      const index = this.tasks.indexOf(task);
      if (index !== -1) {
        this.tasks.splice(index, 1);
        this.saveTasksToLocalStorage();
      }
      this.updateProgress();
    },
    updateProgress() {
      const totalTasks = this.tasks.length;
      const completedTasks = this.tasks.filter(task => task.completed).length;
      this.progress = totalTasks ? Math.round((completedTasks / totalTasks) * 100) : 0;
    },
    saveTasksToLocalStorage() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
    },
    loadTasksFromLocalStorage() {
      const storedTasks = localStorage.getItem('tasks');
      if (storedTasks) {
        this.tasks = JSON.parse(storedTasks);
        this.updateProgress();
      }
    }
  },
  mounted() {
    this.loadTasksFromLocalStorage();
  }
};
</script>

<style>
.v-card {
  margin-bottom: 16px;
}

.progress-container {
  position: relative;
  border: 1px solid black;
  border-radius: 5px;
}

.progress-bar {
  position: relative;
  height: 100%;
  background-color: rgba(0, 255, 255, 0.445);
  z-index: 0;
  border-radius: 5px;
}

.progress-label {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  font-weight: bold;
  color: white;
  z-index: 1;
}

.text-decoration-line-through {
  text-decoration: line-through;
}

</style>
