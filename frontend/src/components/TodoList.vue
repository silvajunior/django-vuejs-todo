<template>
  <div class="p-6 bg-gray-100 min-h-screen">
    <h1 class="text-3xl font-bold text-center text-blue-600 mb-6">Lista de Tarefas</h1>

    <!-- Formulário para adicionar nova tarefa -->
    <form @submit.prevent="addTodo" class="flex items-center gap-3 mb-6 max-w-xl mx-auto">
      <input
        v-model="newTodo.title"
        placeholder="Nova tarefa"
        required
        class="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
      />
      <button
        type="submit"
        class="bg-blue-600 text-white py-2 px-4 rounded-lg hover:bg-blue-700 transition"
      >
      <svg class="w-6 h-6 text-gray-800 dark:text-white" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" viewBox="0 0 24 24">
  <path fill-rule="evenodd" d="M2 12C2 6.477 6.477 2 12 2s10 4.477 10 10-4.477 10-10 10S2 17.523 2 12Zm11-4.243a1 1 0 1 0-2 0V11H7.757a1 1 0 1 0 0 2H11v3.243a1 1 0 1 0 2 0V13h3.243a1 1 0 1 0 0-2H13V7.757Z" clip-rule="evenodd"/>
</svg>
      </button>
    </form>

    <!-- Lista de tarefas -->
    <ul class="space-y-3 max-w-xl mx-auto">
      <li
        v-for="todo in todos"
        :key="todo.id"
        class="flex justify-between items-center bg-white p-4 rounded-lg shadow-md border border-gray-200"
      >
        <span
          @click="editTodo(todo)"
          :style="{ textDecoration: todo.completed ? 'line-through' : 'none' }"
          class="cursor-pointer text-lg font-medium"
        >
          {{ todo.title }}
        </span>
        <div class="flex gap-2">
          <button
            @click="deleteTodo(todo.id)"
            class="bg-red-500 hover:bg-red-600 text-white py-1 px-3 rounded-lg transition"
          >
          <svg class="w-6 h-6 text-gray-800 dark:text-white" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" viewBox="0 0 24 24">
  <path fill-rule="evenodd" d="M2 12C2 6.477 6.477 2 12 2s10 4.477 10 10-4.477 10-10 10S2 17.523 2 12Zm5.757-1a1 1 0 1 0 0 2h8.486a1 1 0 1 0 0-2H7.757Z" clip-rule="evenodd"/>
</svg>

          </button>
          <button
  @click="toggleComplete(todo)"
  class="bg-green-500 hover:bg-green-600 text-white py-1 px-3 rounded-lg transition"
>
  <!-- Mostrar ícone de "Completar" -->
  <span v-if="!todo.completed">
<svg class="w-6 h-6 text-gray-800 dark:text-white inline" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2">
  <rect width="20" height="20" x="2" y="2" rx="3" ry="3"></rect>
  </svg>
  </span>
  
  <!-- Mostrar ícone de "Desfazer" e texto -->
  <span v-else>
    <svg class="w-6 h-6 text-gray-800 dark:text-white inline" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" viewBox="0 0 24 24">
  <path fill-rule="evenodd" d="M9 11.293l-3.293-3.293a1 1 0 1 1 1.414-1.414l2.293 2.293 4.293-4.293a1 1 0 1 1 1.414 1.414l-5 5a1 1 0 0 1-1.414 0z" clip-rule="evenodd"/>
  <rect width="20" height="20" x="2" y="2" rx="3" ry="3" fill="none" stroke="currentColor" stroke-width="2"></rect>
</svg>

  </span>
  
  <!-- Mostrar texto "Completar" ou "Desfazer" 
  {{ todo.completed ? "Do it" : "Done" }} 
  -->
  
</button>

        </div>
      </li>
    </ul>

    <!-- Formulário para editar tarefa -->
    <div
      v-if="editingTodo"
      class="mt-8 max-w-xl mx-auto bg-white p-6 rounded-lg shadow-md border border-gray-200"
    >
      <h2 class="text-2xl font-semibold text-gray-800 mb-4">Editar Tarefa</h2>
      <form @submit.prevent="updateTodo" class="space-y-4">
        <input
          v-model="editingTodo.title"
          required
          class="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
        />
        <div class="flex justify-end gap-3">
          <button
            type="submit"
            class="bg-blue-600 text-white py-2 px-4 rounded-lg hover:bg-blue-700 transition"
          >
            Salvar
          </button>
          <button
            @click="cancelEdit"
            class="bg-gray-300 hover:bg-gray-400 text-gray-800 py-2 px-4 rounded-lg transition"
          >
            Cancelar
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      todos: [], // Lista de tarefas
      newTodo: { title: "", completed: false }, // Nova tarefa
      editingTodo: null, // Tarefa em edição
    };
  },
  mounted() {
    this.fetchTodos();
  },
  methods: {
    // Buscar todas as tarefas
    fetchTodos() {
      axios
        .get("http://127.0.0.1:8000/api/todos/")
        .then((response) => {
          this.todos = response.data;
        })
        .catch((error) => {
          console.error("Erro ao buscar tarefas:", error);
        });
    },
    // Adicionar nova tarefa
    addTodo() {
      axios
        .post("http://127.0.0.1:8000/api/todos/", this.newTodo)
        .then((response) => {
          this.todos.push(response.data); // Adiciona a nova tarefa na lista
          this.newTodo.title = ""; // Limpa o campo
        })
        .catch((error) => {
          console.error("Erro ao adicionar tarefa:", error);
        });
    },
    // Excluir uma tarefa
    deleteTodo(id) {
      axios
        .delete(`http://127.0.0.1:8000/api/todos/${id}/`)
        .then(() => {
          this.todos = this.todos.filter((todo) => todo.id !== id); // Remove da lista localmente
        })
        .catch((error) => {
          console.error("Erro ao remover tarefa:", error);
        });
    },
    // Alternar status de completado
    toggleComplete(todo) {
      axios
        .patch(`http://127.0.0.1:8000/api/todos/${todo.id}/`, {
          completed: !todo.completed,
        })
        .then((response) => {
          todo.completed = response.data.completed; // Atualiza o status localmente
        })
        .catch((error) => {
          console.error("Erro ao atualizar tarefa:", error);
        });
    },
    // Iniciar edição de uma tarefa
    editTodo(todo) {
      this.editingTodo = { ...todo }; // Cria uma cópia para edição
    },
    // Atualizar tarefa editada
    updateTodo() {
      axios
        .put(`http://127.0.0.1:8000/api/todos/${this.editingTodo.id}/`, this.editingTodo)
        .then((response) => {
          const index = this.todos.findIndex((todo) => todo.id === response.data.id);
          this.todos[index] = response.data; // Atualiza a lista localmente
          this.editingTodo = null; // Limpa o formulário de edição
        })
        .catch((error) => {
          console.error("Erro ao atualizar tarefa:", error);
        });
    },
    // Cancelar edição
    cancelEdit() {
      this.editingTodo = null;
    },
  },
};
</script>
