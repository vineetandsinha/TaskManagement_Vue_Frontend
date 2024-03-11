<template>
    <div class="todo">
      <button @click="openNewTaskModal">New Task</button>
      <table class="table">
        <thead>
          <tr>
            <th>Title</th>
            <th>Description</th>
            <th>Status</th>
            <th>Deadline</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="task in tasks" :key="task.id" class="table-body">
            <td>{{ task.title }}</td>
            <td>{{ task.description }}</td>
            <td>{{ task.status }}</td>
            <td>{{ task.deadline }}</td>
            <td>
                <button @click="openEditModal(task)">Edit</button>
                <button @click="deleteTask(task)">Delete</button>
          </td>
          </tr>
        </tbody>
      </table>

      
    </div>

    <!-- Create New Task Modal -->
    <div class="modal" v-if="isNewModalOpen">
    <div class="modal-content">
        <h2>New Task</h2>
        <input type="text" v-model="newTask.title">
        <input type="text" v-model="newTask.description">
        <input type="text" v-model="newTask.status">
        <input type="date" v-model="newTask.deadline">
        <button @click="newRecord">Save</button>
        <button @click="closeModal">Cancel</button>
    </div>
    </div>

   <!-- Edit Modal -->
    <div class="modal" v-if="isEditModalOpen">
    <div class="modal-content">
        <h2>Edit Task</h2>
        <input type="text" v-model="editedTask.title">
        <input type="text" v-model="editedTask.description">
        <input type="text" v-model="editedTask.status">
        <input type="date" v-model="editedTask.deadline">
        <button @click="saveChanges">Save</button>
        <button @click="closeModal">Cancel</button>
    </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  import Echo from 'laravel-echo';

  export default {
    name: 'vueTask',
    mounted() { 
        const echo = new Echo({
        broadcaster: 'pusher',
        key: process.env.MIX_PUSHER_APP_KEY,
        cluster: process.env.MIX_PUSHER_APP_CLUSTER,
        encrypted: true
    });

    echo.channel('tasks')
      .listen('TaskCreated', (event) => {
        // const task = event.task;
        console.log('New task created:', event.task);
        // Update component state or perform other actions
      });



    },
   
    data() {
        return {
            tasks: [],
            newTask : { title: '', description: '', status: '', deadline: '' },
            editedTask: null, // Track the currently edited task
            isEditModalOpen: false, // Initialize the modal flag to false
            isNewModalOpen : false
        };
    },
    created(){
        this.fetchTasks();
    },
    methods: {
        fetchTasks() {
            axios.get('http://localhost:8000/vueTask/')
            .then(response => {
                this.tasks = response.data;
                
            })
            .catch(error => {
                console.error('Error fetching tasks:', error);
            });
        },
        openNewTaskModal() {
            this.newTask = { title: '', description: '', status: '', deadline: '' };
            this.isNewModalOpen = true; // Open the modal
        },

        openEditModal(task) {
            this.editedTask = { ...task };
            this.isEditModalOpen = true;
        },

        deleteTask(task) {
            axios.delete(`http://localhost:8000/vueTask/${task.id}`)
            .then(response => {
            // Handle successful deletion
            console.log('Task deleted:', response.data);
            // Optionally, you can update the tasks list after deletion
            this.fetchTasks(); // Assuming you have a fetchTasks method to fetch updated tasks
        })
        .catch(error => {
            // Handle deletion error
            console.error('Error deleting task:', error);
        });
        },

         // Method to save changes to the task
        saveChanges() {
            axios.put(`http://localhost:8000/vueTask/${this.editedTask.id}`, this.editedTask)
            .then(response => {
                console.log('Task edited:', response.data);
                this.fetchTasks();
                this.isEditModalOpen = false; // Close the modal after successful edit
            })
            .catch(error => {
                console.error('Error updating task:', error);
            });
        },

         // Method to save changes to the task
         newRecord() {
            axios.post(`http://localhost:8000/vueTask/create`, this.newTask)
            .then(response => {
                console.log('Task added:', response.data);
                this.fetchTasks();
                this.isNewModalOpen = false; // Close the modal after successful edit
            })
            .catch(error => {
                console.error('Error saving task:', error);
            });
        },

        // Method to close the modal without saving changes
        closeModal() {
            this.isEditModalOpen = false; // Close the modal
            this.isNewModalOpen = false; // Close the modal
  }

    }
  }

  </script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
    .table {
    width: 100%;
    border-collapse: collapse;
    }

    .table th,
    .table td {
    border: 1px solid #ddd;
    padding: 8px;
    text-align: left;
    }

    .table th {
    background-color: #f2f2f2;
    }

    .table-body {
    background-color: #fff;
    }

    .table-body:hover {
    background-color: #f2f2f2;
    }

    .modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5); /* Semi-transparent overlay */
    z-index: 1000; /* Ensure modal is on top of other content */
    }

    .modal-content {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: white;
    padding: 20px;
    }
  </style>
  