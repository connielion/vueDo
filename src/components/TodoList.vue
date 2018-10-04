<template>
  <div>
      <h3>This is a to-do app made with Vue.js, Sass, Animate.css.</h3>
      <h4>It's my first Vue.js project. <a href="https://connielion.com/" target=_blank>My website.</a></h4>

      <ol class="list">
        <li>Press 'Enter/return' key to add todo item. Empty input will NOT be entered.</li>
        <li>Double-click on item to edit.</li>
        <li>Press 'Escape' key to cancel edit.</li>
        <li>You can filter items using the 'all', 'active' for incompleted items, and 'completed' button at the bottom.</li>
      </ol>

      <input type="text" class="todo-input" placeholder="What needs to be done?" v-model="newTodo" @keyup.enter="addTodo" autofocus>

      <button @click="addTodo" class="addbtn">Add</button>

<!--animate css transition tag-->
<transition-group enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
  <!--loop over todo array-->
    <div v-for="(todo, index) in filteredTodos" :key="todo.id" class="todo-item">

      <div class="todo-l">
          <input type="checkbox" v-model="todo.completed">
    <!--label: double click to enter edit mode-->
          <label class="todo-label" v-if="!todo.editMode" @dblclick="editTodo(todo)" :class="{ done : todo.completed}">
            {{ todo.title }}
          </label>
    <!--press escape key to cancel edit and return to original input-->

          <input type="text" class="todo-edit" v-model="todo.title" v-focus v-else @blur="doneEdit(todo)" @keyup="doneEdit(todo)" @keyup.esc="cancelEdit(todo)">
      </div>
      <!--end todo-l-->
      <div class="remove" @click="removeTodo(index)">
          &times; <!--'x'-->
      </div>
      <!--end x-->
      </div><!--end todo-item-->
    </transition-group>
            <div class="count-container">
              <div><label for="">
                <input type="checkbox" :checked="!noRemaining" @change="checkAll">
                 Check All</label></div>
              <div>{{ remaining }} items left.</div>
            </div><!--end count container-->

            <div class="count-container">
              <div>
                <button :class="{ active: filter == 'all'}" @click="filter = 'all'">All</button>
                <button :class="{ active: filter == 'active'}" @click="filter = 'active'">Active</button>
                <button :class="{ active: filter == 'completed'}" @click="filter = 'completed'">Completed</button>
              </div>
              <div>
                <transition name="fade">
                  <button v-if="showClearBtn" @click="clearCompleted">
                    Clear Completed
                  </button>
                </transition>
              </div>
            </div>
    </div><!--end outer div-->
</template>

<script>
const STORAGE_KEY = 'todo-storage';

export default {
  name: 'todo-list',
  data () {
    return {
      newTodo: ' ',
      todoID: 3,
      filter: 'all',
      beforeEditCache: '',
      todos: [],
    }
  },
  computed: { // no data mutation, no param
    remaining() {
      return this.todos.filter(todo => !todo.completed).length;
    },
    noRemaining() {
      return this.remaining !== 0;
    },
    filteredTodos() {
        if (this.filter == 'all') {
          return this.todos
        }
        else if (this.filter == 'active')
        {
          return this.todos.filter(todo => !todo.completed);
        }
        else if (this.filter =='completed')
        {
          return this.todos.filter(todo => todo.completed);
        }
        return this.todos;
    },
    showClearBtn() {
      return this.todos.filter(todo => todo.completed).length > 0;
    }
  },
  directives: {
    focus: {
      // directive definition
      inserted: function (el) {
        el.focus()
      }
    }
},
/* created() {
  this.todos = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');
},*/
  methods: {
    // add todo item
    addTodo() {
      // check if input is empty
      if (this.newTodo.trim().length == 0) {
          return;
      }
      this.todos.push({ // push
        id: this.todoID,
        title: this.newTodo,
        completed: false,

      })
      this.newTodo='';
      this.todoID++;
      // localStorage.setItem(STORAGE_KEY, JSON.stringify(this.todos));
    },
    // edit todo
    editTodo(todo) {
      this.beforeEditCache = todo.title;
      todo.editMode = true;
    },
    // turn off edit mode
    doneEdit(todo) {
          if (todo.title.trim() == '') {
         todo.title = this.beforeEditCache
      }
        todo.editMode = false;
    },
    // cancel edit
    cancelEdit(todo) {
        todo.title = this.beforeEditCache;
        todo.editMode = false;
    },
    // remove todo item
    removeTodo(index) {
      this.todos.splice(index, 1)
    },
    // check all items
    checkAll() {
      this.todos.forEach((todo) => todo.completed = event.target.checked);
    },
    // clear completed list
    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed)
    }



  } // end methods
}
</script>






<style lang="scss">
@import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.0/animate.css");
@import url('https://fonts.googleapis.com/css?family=Schoolbell');

  .todo-input {
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    border: 1px solid lightgreen;
    border-radius: 4px;
    margin-bottom: 16px;

    &:focus {
      outline: 0;
    }
  }

  .todo-item {
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    animation-duration: .3s;
    border: 1px solid #ccc;
    border-radius: 8px;
    padding: 5px;
    margin-top: 5px;
    font-size: 16px;
    font-family: 'Schoolbell', cursive;
  }

  .remove {
    cursor: pointer;
    margin-left: 14px;

    &:hover {
      color: red;
      font-weight: bold;
      border: 1px solid black;
      border-radius: 50%;
    }
  }
  .todo-l {
    display: flex;
    align-items: center;
  }

  .todo-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
  }

  .todo-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100;
    padding: 10px;
    border: 2px solid #ccc;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;

    &:focus {
      outline: none;
    }
  }

  .done {
    text-decoration: line-through;
    color: #ddd;
  }

  .count-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgray;
    padding-top: 14px;
    margin-bottom: 14px;
  }

  .active {
    background: rgb(56, 94, 150);
    color: #fff;
  }

  .fade-enter-active, .fade-leave-active {
    transition: opacity .2s;
  }

  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
  .list {
    font-family: 'Arial';
    font-size: 14px;
    list-style-type: none;
  }
  button {
    border: 1px solid black;
    border-radius: 4px;
    font-size: 18px;
    background: lightcyan;
  }
  .addbtn {
    background: rgb(56, 94, 150);
    color: #fff;
  }

</style>
