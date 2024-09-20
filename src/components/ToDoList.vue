<template>
  <v-card class="pa-4" color="secondary">
    <!-- Section 1: Daily Progress -->
    <v-row>
      <v-col cols="12" md="4">
        <v-card class="pa-4 flex-grow-1 bg">
          <v-card-title class="headline">Daily Progress</v-card-title>
          <v-divider></v-divider>
          <div class="progress-container mb-4">
            <v-progress-linear 
              :value="progress" 
              height="25"
              class="bar" :style="{ width: progress + '%' }" 
            >
              <template v-slot:default>
                <span class="progress-label">{{ Math.round(progress) }}%</span>
              </template>
            </v-progress-linear>
          </div>
          <v-card-title>Categories</v-card-title>
          <v-divider></v-divider>
          <v-list class="bg">
            <v-list-item @click="selectCategory('Personal')">
              <v-list-item-content>Personal</v-list-item-content>
            </v-list-item>
            <v-list-item @click="selectCategory('Work')">
              <v-list-item-content>Work</v-list-item-content>
            </v-list-item>
            <v-list-item @click="selectCategory('Health')">
              <v-list-item-content>Health</v-list-item-content>
            </v-list-item>
            <v-list-item @click="selectCategory('Social')">
              <v-list-item-content>Social</v-list-item-content>
            </v-list-item>
          </v-list>
        </v-card>
      </v-col>

      <!-- Section 2: Add Task -->
      <v-col cols="12" md="8">
        <v-card class="pa-4 flex-grow-1 bg">
          <v-card-title class="headline">Add To List</v-card-title>
          <v-divider></v-divider>
          <!-- Alert for missing fields -->
          <v-alert v-if="showAlert" type="error" dense class="small-alert">
            {{ alertMessage }}
          </v-alert>
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
            <v-text-field
              v-model="newTaskDeadline"
              label="Deadline"
              type="date"
              dense
              hide-details
            />
            <v-btn @click="addTask" class="mt-5 ms-5 bg">Add to List</v-btn>
          </v-form>
        </v-card>
      </v-col>
    </v-row>

    <!-- Section 3: To-Do List Header -->
    <v-row class="align-center mt-4 justify-space-between" style="max-width: 500px; margin: auto;">
      <v-col class="d-flex justify-center" cols="4">
        <div class="headline" id="clock">{{ currentTime }}</div>
      </v-col>
      <v-col class="d-flex justify-center" cols="4">
        <v-card-title class="headline text-center">To-Do-List</v-card-title>
      </v-col>
      <v-col class="d-flex justify-center" cols="4">
        <span v-if="selectedCategory" class="headline cat">Category: {{ selectedCategory }}</span>
      </v-col>
    </v-row>
    <v-divider></v-divider>

    <!-- Section 4: To-Do List -->
    <v-row v-if="filteredTasks.length === 0" class="text-center">
      <v-col>
        <v-card class="pa-4 bg">
          <v-card-title>No tasks yet</v-card-title>
        </v-card>
      </v-col>
    </v-row>
    <v-row v-else>
      <v-col v-for="task in filteredTasks" :key="task.id" cols="12" md="4">
        <v-card class="pa-4 mb-3 bg">
          <!-- Check if in editing mode -->
          <v-card-title v-if="task.isEditing">
            <v-text-field v-model="task.title" label="Task Title" dense hide-details />
          </v-card-title>
          <v-card-title v-else>
            {{ task.title }}
          </v-card-title>

          <v-card-subtitle>
            Category: {{ task.category }}
          </v-card-subtitle>

          <v-card-subtitle v-if="task.isEditing">
            <v-text-field v-model="task.deadline" type="date" label="Deadline" dense hide-details />
          </v-card-subtitle>
          <v-card-subtitle v-else>
            Deadline: {{ task.deadline }}
          </v-card-subtitle>

          <v-divider></v-divider>
          <v-card-subtitle>Details:</v-card-subtitle>
          <v-divider></v-divider>

          <v-list class="bg" dense v-if="!task.isEditing">
            <v-list-item-group>
              <v-list-item v-for="detail in task.details" :key="detail">
                <v-list-item-content>
                  <v-list-item-title>
                    <v-icon left>mdi-bullet</v-icon>
                    {{ detail }}
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list-item-group>
          </v-list>

          <v-textarea 
            v-if="task.isEditing" 
            v-model="task.detailsInput" 
            label="Task Details" 
            rows="3" 
            dense 
            hide-details
          />

          <v-divider></v-divider>
          <v-card-actions>
            <v-btn v-if="!task.isEditing" @click="editTask(task)" icon>
              <v-icon>mdi-pencil</v-icon>
            </v-btn>
            <v-btn v-if="task.isEditing" @click="saveTask(task)" icon color="green">
              <v-icon>mdi-content-save</v-icon>
            </v-btn>
            <v-btn @click="toggleComplete(task)" icon>
              <v-icon :color="task.completed ? 'green' : 'black'">mdi-check-circle</v-icon>
            </v-btn>
            <v-btn @click="deleteTask(task)" icon>
              <v-icon>mdi-delete</v-icon>
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-card>
</template>
<script>
export default {
  data() {
    return {
      newTaskTitle: '',
      newTaskDetails: '',
      newTaskDeadline: null,
      selectedCategory: null,
      tasks: [],
      progress: 0,
      categories: ['Personal', 'Work', 'Health', 'Social'],
      showAlert: false,
      alertMessage: '',
      currentTime: '',
    };
  },
  computed: {
    filteredTasks() {
      if (this.selectedCategory) {
        return this.tasks.filter(task => task.category === this.selectedCategory);
      }
      return this.tasks;
    },
  },
  methods: {
    selectCategory(category) {
      this.selectedCategory = category;
    },
    addTask() {
      if (!this.newTaskTitle.trim()) {
        this.showAlertMessage('Please enter a task title.');
      } else if (!this.newTaskDetails.trim()) {
        this.showAlertMessage('Please enter task details.');
      } else if (!this.selectedCategory) {
        this.showAlertMessage('Please select a category.');
      } else if (!this.newTaskDeadline) {
        this.showAlertMessage('Please select a deadline.');
      } else {
        this.tasks.push({
          id: Date.now(),
          title: this.newTaskTitle.trim(),
          details: this.newTaskDetails.trim().split('\n'),
          detailsInput: this.newTaskDetails.trim(),
          category: this.selectedCategory,
          deadline: this.newTaskDeadline,
          completed: false,
          isEditing: false,  // Track if task is being edited
        });
        this.newTaskTitle = '';
        this.newTaskDetails = '';
        this.newTaskDeadline = null;
        this.selectedCategory = null;
        this.showAlert = false;
        this.saveTasksToLocalStorage();
        this.updateProgress();
      }
    },
    editTask(task) {
      task.isEditing = true;
      task.detailsInput = task.details.join('\n'); // Prepare for editing details
    },
    saveTask(task) {
      task.isEditing = false;
      task.details = task.detailsInput.split('\n'); // Save the edited details
      this.saveTasksToLocalStorage();
    },
    toggleComplete(task) {
      task.completed = !task.completed;
      this.saveTasksToLocalStorage();
      this.updateProgress();
    },
    deleteTask(task) {
      this.tasks = this.tasks.filter(t => t.id !== task.id);
      this.saveTasksToLocalStorage();
      this.updateProgress();
    },
    updateProgress() {
      const completedTasks = this.tasks.filter(task => task.completed).length;
      const totalTasks = this.tasks.length;
      this.progress = totalTasks ? (completedTasks / totalTasks) * 100 : 0;
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
    },
    showAlertMessage(message) {
      this.alertMessage = message;
      this.showAlert = true;
      setTimeout(() => {
        this.showAlert = false;
      }, 3000);
    },
    updateClock() {
      const now = new Date();
      this.currentTime = now.toLocaleTimeString();
    }
  },
  mounted() {
    this.loadTasksFromLocalStorage();
    this.updateClock();
    setInterval(this.updateClock, 1000); // Update the clock every second
  }
};
</script>
<style scoped>
.progress-container {
  position: relative;
  border: 1px solid #FF8A00; /* Deep Orange */
  border-radius: 5px;
}

.progress-label {
  color: #FFF; /* White for contrast */
}

.bar {
  background-color: #FFCB77; /* Light Gold */
  border-radius: 5px;
}

.bg {
  background-color: #FFF3E0; /* Light Cream */
  border-radius: 5px;
}

.small-alert {
  background-color: #F44336; /* Red for error alerts */
  color: white;
  padding: 8px;
  font-size: 12px;
  line-height: 1.2;
}

#clock {
  font-size: 20px;
  font-weight: bold;
}

.cat {
  font-size: 15px;
  font-weight: bold;
}

.v-btn {
  background-color: #FFB74D; /* Golden Yellow for buttons */
  color: white;
}

</style>

