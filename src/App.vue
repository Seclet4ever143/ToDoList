<template>
  <v-app>
    <v-container fluid>
      <v-row class="d-flex align-stretch">
        <DailyProgress :progress="progress" @select-category="selectCategory" />

        <AddToList 
          :categories="categories" 
          :newTaskTitle="newTaskTitle" 
          :newTaskDetails="newTaskDetails" 
          :selectedCategory="selectedCategory"
          @update:newTaskTitle="newTaskTitle = $event"
          @update:newTaskDetails="newTaskDetails = $event"
          @update:selectedCategory="selectedCategory = $event"
          @add-task="addTask"
        />
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
import DailyProgress from './components/DailyProgress.vue';
import AddToList from './components/AddToList.vue';
import ToDoList from './components/ToDoList.vue';

export default {
  components: {
    DailyProgress,
    AddToList,
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
        this.selectedCategory = null;
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
