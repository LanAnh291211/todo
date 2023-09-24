<template>
  <AddTodo @add-todo="addTodo" />
  <FindToDo />
  <TodoItem
    v-for="todo in todos"
    :key="todo.id"
    :todoProps="todo"
    @item-completed="checkCompleted"
    @delete-item="deleteTodo"


  />
  <button class="loadmoreBtn" @click="getLoadmoreTodos" v-if="showLoadMoreButton" >Xem Thêm</button>


</template>

<script>
import { ref } from 'vue'

import axios from 'axios'

import TodoItem from './TodoItem'
import AddTodo from './AddTodo'
import { computed } from 'vue'


export default {
  name: 'TodoList',
  components: { TodoItem, AddTodo },



 
  setup() {
    const todos = ref([])
    const allTodos = ref([])

    const getAllTodos = async () => {
      try {
        const res = await axios.get(
          'https://jsonplaceholder.typicode.com/todos'
        )
        allTodos.value = res.data
          console.log(allTodos.value.length)
      } catch (error) {
        console.log(error)
      }
    }
    

    const getTodoList = async () => {
      try {
        const res = await axios.get(
          'https://jsonplaceholder.typicode.com/todos?_limit=5'
        )
        todos.value = res.data
      } catch (error) {
        console.log(error)
      }
    }


    getTodoList()
    getAllTodos()

    const checkCompleted = id => {
      todos.value = todos.value.map(todo => {
        if (todo.id === id) todo.completed = !todo.completed
        return todo
      })
    }

    const deleteTodo = async id => {
      try {
        await axios.delete(`https://jsonplaceholder.typicode.com/todos/${id}`)
        todos.value = todos.value.filter(todo => todo.id !== id)
      } catch (error) {
        console.log(error)
      }
    }

    const addTodo = async newTodo => {
      try {
        const res = await axios.post(
          'https://jsonplaceholder.typicode.com/todos',
          newTodo
        )
        todos.value.push(res.data)
      } catch (error) {
        console.log(error)
      }
    }
        const getLoadmoreTodos = async () => {
      try {
        const res = await axios.get(
            `https://jsonplaceholder.typicode.com/todos?_limit=${todos.value.length + 5}`
        )
        
        todos.value = res.data
      } catch (error) {
        console.log(error)
      }
    }
     const  showLoadMoreButton = computed(() => {
      return todos.value.length < allTodos.value.length
    })

  


    return {
      todos,
      allTodos,
      checkCompleted,
      deleteTodo,
      addTodo,
      getLoadmoreTodos,
      showLoadMoreButton
    }
  },

}
</script>

<style>
.loadmoreBtn {
  background-color: #4CAF50; /* Green */
  border: none;
  color: white;
  padding: 10px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin-left: 50%;

}
</style>
