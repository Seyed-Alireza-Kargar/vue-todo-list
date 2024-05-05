<template>
  <div class="container">
    <h1 class="title">Todo List</h1>
    <div class="input-container">
      <input type="text" v-model="newTodo" @keyup.enter="addTodo" class="input-field">
      <button @click="addTodo" class="add-button">Add</button>
    </div>
    <ul class="todo-list">
      <li v-for="(todo, index) in todos" :key="index" class="todo-item">
        <input 
          type="text" 
          :value="todo" 
          :readonly="editingIndex !== index" 
          @click="startEditing(index)" 
          class="todo-input"
        >
        <div class="button-container">
          <button @click="openDialog(index, 'edit')" class="update-button">
            <span>&#9998;</span>
          </button>
          <button @click="openDialog(index, 'delete')" class="delete-button">
            <span>&#128465;</span>
          </button>
        </div>
      </li>
    </ul>
    <div v-if="dialogVisible" class="dialog">
      <div class="dialog-content">
        <h2 v-if="dialogType === 'edit'">Edit Todo</h2>
        <h2 v-else-if="dialogType === 'delete'">Delete Todo</h2>
        <h2 v-else>Add Todo</h2>
        <input v-if="dialogType === 'edit' || dialogType === 'add'" v-model="editedTodo" type="text" class="dialog-input">
        <div class="dialog-buttons">
          <button @click="closeDialog">Cancel</button>
          <button v-if="dialogType === 'edit'" @click="confirmEdit">Update</button>
          <button v-else-if="dialogType === 'add'" @click="confirmAdd">Add</button>
          <button v-else @click="confirmDelete">Delete</button>
        </div>
      </div>
    </div>
    <div v-if="loading" class="loading-overlay">
      <div class="loader"></div>
      <p>Loading...</p>
    </div>
    <div v-if="successMessage" class="success-message">
      <p>{{ successMessage }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      todos: [],
      newTodo: "",
      editingIndex: -1,
      dialogVisible: false,
      dialogType: "",
      editedTodo: "",
      loading: false,
      successMessage: ""
    };
  },
  methods: {
    addTodo() {
      this.loading = true;
      setTimeout(() => {
        if (this.newTodo.trim()) {
          this.todos.push(this.newTodo.trim());
          this.newTodo = "";
          this.loading = false;
          this.showSuccessMessage("Task added successfully!");
        }
      }, 2000);
    },
    deleteTodo() {
      if (this.dialogType === "delete") {
        this.todos.splice(this.editingIndex, 1);
        this.closeDialog();
        this.showSuccessMessage("Task deleted successfully!");
      }
    },
    startEditing(index) {
      this.editingIndex = index;
    },
    openDialog(index, type) {
      this.editedTodo = this.todos[index];
      this.dialogType = type;
      this.dialogVisible = true;
    },
    closeDialog() {
      this.dialogVisible = false;
    },
    confirmEdit() {
      if (this.dialogType === "edit") {
        if (this.editedTodo.trim()) {
          this.todos.splice(this.editingIndex, 1, this.editedTodo.trim());
          this.editingIndex = -1;
          this.dialogVisible = false;
          this.showSuccessMessage("Task updated successfully!");
        }
      }
    },
    confirmAdd() {
      if (this.dialogType === "add") {
        if (this.editedTodo.trim()) {
          this.todos.push(this.editedTodo.trim());
          this.editedTodo = "";
          this.dialogVisible = false;
          this.showSuccessMessage("Task added successfully!");
        }
      }
    },
    confirmDelete() {
      if (this.dialogType === "delete") {
        this.todos.splice(this.editingIndex, 1);
        this.editingIndex = -1;
        this.dialogVisible = false;
        this.showSuccessMessage("Task deleted successfully!");
      }
    },
    showSuccessMessage(message) {
      this.successMessage = message;
      setTimeout(() => {
        this.successMessage = "";
      }, 3000);
    }
  }
};
</script>

<style scoped>
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
}

.title {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 20px;
}

.input-container {
  display: flex;
  margin-bottom: 10px;
}

.input-field {
  flex: 1;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px 0 0 5px;
}

.add-button {
  padding: 10px 20px;
  background-color: #007bff;
  border: none;
  color: #fff;
  border-radius: 0 5px 5px 0;
  cursor: pointer;
}

.todo-list {
  list-style: none;
  padding: 0;
}

.todo-item {
  display: flex;
  align-items: center;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-bottom: 10px;
}

.todo-input {
  flex: 1;
  padding: 10px;
  border: none;
  border-radius: 5px 0 0 5px;
}

.button-container {
  display: flex;
}

.update-button,
.delete-button {
  padding: 10px;
  border: none;
  background-color: transparent;
  cursor: pointer;
}

.update-button span,
.delete-button span {
  font-size: 18px;
}

.dialog {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
}

.dialog-content {
  background-color: #fff;
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
  text-align: center;
}

.dialog-input {
  width: 75%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.dialog-buttons {
  display: flex;
  justify-content: center;
}

.dialog-buttons button {
  padding: 10px 20px;
  margin: 0 5px;
  border: none;
  background-color: #007bff;
  color: #fff;
  border-radius: 5px;
  cursor: pointer;
}

.dialog-buttons button:first-child {
  background-color: #dc3545;
}

.loading-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(255, 255, 255, 0.8);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.loader {
  border: 8px solid #f3f3f3;
  border-top: 8px solid #3498db;
  border-radius: 50%;
  width: 50px;
  height: 50px;
  animation: spin 2s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.success-message {
  position: fixed;
  top: 20px;
  left: 50%;
  transform: translateX(-50%);
  background-color: #28a745;
  color: #fff;
  padding: 10px 20px;
  border-radius: 5px;
  animation: slideInDown 0.5s ease-in-out;
}

@keyframes slideInDown {
  0% {
    transform: translateY(-100%);
  }
  100% {
    transform: translateY(0);
  }
}
</style>
