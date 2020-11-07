<template lang="pug">
  div#app
    div {{timestamp}}
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
    ul.list
      li(
        v-for="(item, key) in filterData"
        @click="test(item)"
        :class="{'complete':item.completed}"
      )
        div.title-area
          span.title {{item.text}}
          div.button-area
            button.delete(@click="remove(key)") x
        div.time
          span Created: {{item.created}}
          span Finished: {{item.finished}}
</template>

<script>
  export default {
    name: 'App',
    data() {
      return {
        page: 'all',
        newTodo: '',
        todos: [],
        search: '',
        timestamp: ''
      }
    },
    methods: {
      add() {
        this.todos.push({ 
          text: this.newTodo,
          completed: false,
          created: this.timestamp,
          finished: ''
        });
        this.newTodo = '';
      },
      remove(key) {
        this.todos.splice(key, 1);
      },
      getNow() {
        const today = new Date();
        const date = today.getFullYear()+'/'+(today.getMonth()+1)+'/'+today.getDate();
        const time = today.getHours() + ":" + today.getMinutes() + ":" + today.getSeconds();
        const dateTime = date +' '+ time;
        this.timestamp = dateTime;
      },
      test(item) {
        item.completed = !item.completed
        item.finished = item.completed ? this.timestamp : ''
        
      }
    },
    computed: {
      filterData() {
        if(this.page == 'all') {
          return　this.search == '' ? this.todos : this.todos.filter(item => {return item.text.toLowerCase().includes(this.search.toLowerCase());})
        } else if(this.page == 'finish') {
          let data = this.todos.filter(item => {
            return item.completed == true;
          })
          return this.search == '' ? data : data.filter(item => {return item.text.toLowerCase().includes(this.search.toLowerCase());})
        } else {
          let data = this.todos.filter(item => {
            return item.completed == false;
          })
          return this.search == '' ? data : data.filter(item => {return item.text.toLowerCase().includes(this.search.toLowerCase());})
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
    },
    created() {
      setInterval(this.getNow, 1000);
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
        flex-direction: column
        width: 100%
        margin: 5px 0
        text-align: left
        border: 1px solid #e3e6e9
        box-sizing: border-box
        & .title-area 
          position: relative
          padding: 0 10px
          line-height: 2
          background: #f1f4f7
          & .button-area 
            position: absolute
            top: 0
            right: 10px
            display: flex
            align-items: center
            height: 100%
        & .time
          padding: 0 10px
          color: #7d7d7d
          line-height: 1.5
          & span
            display: inline-block
            width: 40%
            margin-right: 30px
      & li.complete
        & .title-area
          background: #c2f1db
          text-decoration: line-through
        
    button
      color: #fff
      font-weight: bold
      border: 0
      &.add
        width: 10%
        line-height: 2
        background: #65adee
      &.delete
        background: #f16769
</style>
