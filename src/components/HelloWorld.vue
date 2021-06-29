<template>
  <div class="container">

    <div class="d-flex justify-content-between m-2">
      <input class="w-75" type="text" v-model="todoName">
      <button class="btn btn-primary" @click="addMore">
        Add More
      </button>
    </div>

    <div v-bind:key="todo.name" v-for="(todo, index) in todos">
      <div :class="[todo.bg_class]" class="pointer d-flex justify-content-between border border-1 m-2 p-3">
        <p :class="{'completed': todo.is_completed}" class="flex-grow-2 text-align-left" @click="toggleCompleted(index)">{{ todo.name }}</p>
        <button class="btn btn-danger" @click="deleteTodo(index, todo)">Delete</button>
      </div>
    </div>
  </div>
</template>

<script>
import Vue from "vue";

export default {
  name: 'HelloWorld',
  data() {
    return {
      todoName: '',
      colors: [
        'bg-danger',
          'bg-info',
          'bg-warning'
      ],
      todos: []
    }
  },
  mounted() {
    this.loadTodos()
  },
  methods: {
    loadTodos() {
      /* eslint-disable */
      fetch('http://127.0.0.1:8000/api/todos')
          .then((res) => res.json())
      .then(({ todos }) => {
        this.todos = todos
      })
    },

    addMore() {
      if (! this.todoName) {
        alert('Please add alert name')
        return;
      }
      const todo = {
        name: this.todoName,
        bg_class: this.colors[Math.floor(Math.random() * this.colors.length)]
      }

      fetch('http://127.0.0.1:8000/api/todos', {
        method: 'post',
        body: JSON.stringify(todo),
      }).then(() => this.loadTodos())


      // Vue.set(this.todos, this.todos.length, todo)

      this.todoName = ''
    },
    toggleCompleted(index) {
      const todo = this.todos[index]
      todo.is_completed = ! todo.is_completed;

      fetch(`http://127.0.0.1:8000/api/todos/${todo.id}`, {
        method: 'put',
        body: JSON.stringify({ is_completed: todo.is_completed }),
      })

    },
    deleteTodo(index, todo) {
      fetch(`http://127.0.0.1:8000/api/todos/${todo.id}`, {
        method: 'delete',
        body: JSON.stringify(todo),
      })

      this.todos.splice(index, 1)
    }
  }
}
</script>

<style scoped>
.pointer {
  cursor: pointer;
}
.completed {
  text-decoration: line-through;
}

.flex-grow-2 {
  flex-grow: 2;
}

.text-align-left {
  text-align: left;
}
</style>
