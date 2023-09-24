<script setup>
import { ref, onMounted } from 'vue';
import './listTask.scss'; 

const tasks = ref([]);
const newTask = ref('');
const editedTaskIndex = ref(-1);

const handleAddOrEditTask = () => {
  if (newTask.value.trim() === '') return;

  if (editedTaskIndex.value === -1) {
    tasks.value.push({ name: newTask.value, completed: false });
  } else {
    tasks.value[editedTaskIndex.value].name = newTask.value;
    editedTaskIndex.value = -1;
  }

  newTask.value = '';
  saveToLocalStorage();  
};

const handleEditTask = (index) => {
  editedTaskIndex.value = index;
  newTask.value = tasks.value[index].name;
};

const handleDeleteTask = (index) => {
  tasks.value.splice(index, 1);
  saveToLocalStorage();  
};

const handleTaskCompletion = (index) => {
  tasks.value[index].completed = !tasks.value[index].completed;
  saveToLocalStorage();
};

const saveToLocalStorage = () => {
  localStorage.setItem('tasks', JSON.stringify(tasks.value));
};

onMounted(() => {
  const savedTasks = localStorage.getItem('tasks');
  if (savedTasks) {
    tasks.value = JSON.parse(savedTasks);
  }
});



</script>

<template>
  <div>
    <input v-model="newTask" placeholder="Add a new task" />
    <button @click="handleAddOrEditTask">{{ editedTaskIndex === -1 ? 'Add Task' : 'Save' }}</button>
  </div>

  <table class="custom-table">
    <thead>
      <tr>
        <th>Task</th>
        <th>Complete/ Incomplete</th>
        <th>Edit/Delete</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(task, index) in tasks" :key="index" :class="{ 'completed-task': task.completed }">
        <td>
          <span v-if="editedTaskIndex !== index">{{ task.name }}</span>
        </td>
        <td>
          <input type="checkbox" :checked="task.completed" @change="handleTaskCompletion(index)" />
        </td>
        <td>
          <button @click="handleEditTask(index)" :disabled="editedTaskIndex !== -1">Edit</button>
          <button @click="handleDeleteTask(index)" :disabled="editedTaskIndex !== -1">Delete</button>
        </td>
      </tr>
    </tbody>
  </table>

</template>

