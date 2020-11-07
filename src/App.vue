<template lang="pug">
  div#app
    h1 Tasks
      span ({{notCompleted}})
    ul.tab
      li(@click="page='all'" :class="{'active':page=='all'}") ALL({{allTasks}})
      li(@click="page='finish'" :class="{'active':page=='finish'}") FINISH
      li(@click="page='not'" :class="{'active':page=='not'}") NOT YET
    div.box
      input(v-model="search")
    div.box
      input(v-model="newTodo" @keydown.enter="add")
      button.add(@click="add()") + Add
    ul.list(v-if="search==''")
      li(
        v-for="(item, key) in filterData"
        @click="item.completed = !item.completed"
        :class="{'complete':item.completed}"
      )
        span {{item.text}}
        button.delete(@click="remove(key)") x
    ul.list(v-else)
      li(
        v-for="(item, key) in searchData"
        @click="item.completed = !item.completed"
        :class="{'complete':item.completed}"
      )
        span {{item.text}}
        button.delete(@click="remove(key)") x
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      page: 'all',
      newTodo: '',
      todos: [],
      search: ''
    }
  },
  methods: {
    add() {
      this.todos.push({ text: this.newTodo, completed: false });
      this.newTodo = '';
    },
    remove(key) {
      this.todos.splice(key, 1);
    }
  },
  computed: {
    filterData() {
      if(this.page == 'all') {
        return this.todos;
      } else if(this.page == 'finish') {
        return this.todos.filter(item => {
          return item.completed == true;
        })
      } else {
        return this.todos.filter(item => {
          return item.completed == false;
        })
      }
    },
    searchData() {
      if(this.page == 'finish') {
        let data = this.todos.filter(item => {
          return item.completed == true;
        })
        return data.filter(item => {
          return item.text.toLowerCase().includes(this.search.toLowerCase());
        })
      } else if(this.page == 'not') {
        let data = this.todos.filter(item => {
          return item.completed == false;
        })
        return data.filter(item => {
          return item.text.toLowerCase().includes(this.search.toLowerCase());
        })
      } else {
        return this.todos.filter(item => {
          return item.text.toLowerCase().includes(this.search.toLowerCase());
        })
      }
    },
    allTasks() {
      return this.todos.length
    },
    notCompleted() {
      let notcompleted = this.todos.filter(item => {
        return item.completed == false
      })
      return notcompleted.length
    }
  },
  watch: {
  },
  mounted() {
    if(localStorage.getItem('todos')) {
      this.todos = JSON.parse(localStorage.getItem('todos'));
    }
  },
  watch: {
    todos: {
      handler() {
        localStorage.setItem('todos', JSON.stringify(this.todos));
      },
      deep: true
    }
  }
}
</script>

<style lang="sass">
ul, li
  margin: 0
  padding: 0
li
  list-style: none
#app
  width: 600px
  margin: 60px
  padding: 60px
  font-family: Avenir, Helvetica, Arial, sans-serif
  -webkit-font-smoothing: antialiased
  -moz-osx-font-smoothing: grayscale
  text-align: center
  color: #2c3e50
  box-shadow: 5px 5px 5px rgba(0,0,0,0.3)
  h1 span
    font-size: 20px
    color: #575555
  .tab li
    display: inline-block
    min-width: 100px
    line-height: 2
    margin: 0 10px 10px 0
    border: 1px solid #e3e6e9
    cursor: pointer
    &.active
      background: #e3e6e9
  .box, .list
    display: flex
    width:100%
    margin: 5px 0px 10px
    & input
      width:90%
      line-height: 2
  .list
    flex-direction: column
    & li
      display: flex
      width: 100%
      margin: 5px 0
      border: 1px solid #e3e6e9
      box-sizing: border-box
      & span
        width: 95%
        padding-left: 10px
        text-align: left
        line-height: 2
        background: #f1f4f7
    & li.complete span
      text-decoration: line-through
      background: #c2f1db
  button
    color: #fff
    font-weight: bold
    border: 0
    &.add
      width: 10%
      line-height: 2
      background: #65adee
    &.delete
      width: 5%
      background: #f16769
</style>
