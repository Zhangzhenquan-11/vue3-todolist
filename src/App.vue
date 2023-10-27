<script>
import { reactive, toRefs, computed } from 'vue'
export default {
  setup() {
    const state = reactive({
      newTodo: '',
      todos: [],
      status: 'all',
    })


    const remaining = computed(() => state.todos.filter(todo => !todo.completed).length)
    const allDone = computed({
      get: function () {
        return remaining.value === 0
      },
      set: function (value) {
        state.todos.forEach((todo) => {
          todo.completed = value
        })
      }
    })
    const todoList = computed(() => {
      if (state.status === 'all') {
        return state.todos
      } else if (state.status === 'active') {
        return state.todos.filter(todo => !todo.completed)
      } else if (state.status === 'completed') {
        return state.todos.filter(todo => todo.completed)
      }
    })
    const left = computed(() => remaining.value)


    const addTodo = () => {
      if (!state.newTodo) return  // 若输入框输入的值为空不予加入列表中
      state.todos.push({
        id: state.todos.length + 1,
        title: state.newTodo,
        completed: false
      })
      state.newTodo = '' //每次回车加完后重新使输入框的值为空
    }
    const removeTodo = (item) => {
      state.todos = state.todos.filter(todo => todo.id !== item.id)
    }
    const show = (status) =>  {
      state.status = status
    }
    const removeCompleted = () => {
      state.todos = state.todos.filter(todo => !todo.completed)
    }

    return {
      ...toRefs(state),
      allDone,
      todoList,
      left,
      addTodo,
      removeTodo,
      show,
      removeCompleted,
    }
  }
}
</script>

<template>
  <section class="todoapp">
    <header class="header">
      <h1>todolist</h1>
      <input type="text" class="new-todo" placeholder="请输入" @keyup.enter="addTodo" v-model.trim="newTodo">
    </header>

    <section class="main">
      <input type="checkbox" class="toggle-all" id="toggle-all" v-model="allDone">
      <label for="toggle-all">Mark all as complete</label>

      <ul class="todo-list">
        <li class="todo" v-for="(todo) in todoList" :key="todo.id" >
          <div class="view">
            <input type="checkbox" class="toggle" v-model="todo.completed">
            <label>{{ todo.title }}</label>
            <button class="destroy" @click="removeTodo(todo)"></button>
          </div>
        </li>
      </ul>
    </section>

    <footer class="footer" v-show="todos.length" v-cloak>
      <span class="todo-count">
          <strong>{{ left }}</strong> left
        </span>
      <span class="todo-choose">
          <button class="clear-completed todo-do" @click="show('all')">All</button>
          <button class="clear-completed todo-do" @click="show('active')">Active</button>
          <button class="clear-completed todo-do" @click="show('completed')">Completed</button>
        </span>
      <button class="clear-completed" @click="removeCompleted">
        Clear completed
      </button>
    </footer>
  </section>
</template>

<style scoped>
span.todo-choose{
  display: inline-block;
  margin-top: -10px;
}
.todo-choose button.todo-do{
  padding: 10px;
}
button.clear-completed.todo-do{
  width: 90px;
}
</style>
