<template>
  <div class="todo-item">
    <div class="todo-item-left">
      <input type="checkbox" v-model="completed" @change="doneEdit">
      <div v-if="!editing" @dblclick="editTodo" class="todo-item-label" :class="{ completed : completed }">{{todo.title}}</div>
      <input v-else class="todo-item-edit" type="text" v-model="title" @blur="doneEdit" @keyup.enter="doneEdit" @keyup.esc="cancelEdit" v-focus>
    </div>
    <div>
    <button @click="pluralize">Plural</button>
    <span class="remove-item" @click="removeTodo(index)">
      &times;
    </span>
    </div>

  </div>
</template>

<script>
export default {
  name: 'todo-item',
  props: {
    todo: {
      type: Object,
      required: true,
    },
    index: {
      type: Number,
      required: true,
    },
    checkAll: {
      type: Boolean,
      required: true,
    }
  },
  data() {
    return {
      'id': this.todo.id,
      'title': this.todo.title,
      'completed': this.todo.completed,
      'editing': this.todo.editing,
      'beforeEditCache': '',

    }
  },
  created() {
    eventBus.$on('pluralize', this.handlePluralize)
  },

  watch: {
    checkAll() {
      // if (this.checkAll) {
      //   this.completed = true
      // } else {
        this.completed = this.checkAll ? true : this.todo.completed
      }
  },
  directive: {
    focus: {
      inserted: function (el) {
        el.focus()
      }
    }
  },
  methods: {
    removeTodo(id) {
      this.$store.dispatch('deleteTodo', id)
      },
    editTodo() {
      this.beforeEditCache = this.title
      this.editing = true
    },
    doneEdit(id) {
      if (this.title.trim() === '') {
        this.title = this.beforeEditCache
      }
      this.editing = false
      this.$store.dispatch('updateTodo', {
        'id': this.id,
        'title': this.title,
        'completed': this.completed,
        'editing': this.editing,
      })
    },


      // eventBus.$emit('finishedEdit', {
      //   'index': this.id,
      //     'id': this.id,
      //     'title': this.title,
      //     'completed': this.completed,
      //     'editing': this.editing,
      //   }
      // })

    pluralize() {
      eventBus.$emit('pluralize')
    },
    handlePluralize() {
      this.title = this.title + 'S'
      const index = this.$store.state.todos.findIndex(item => item.id == this.id);
      this.$store.state.todos.splice(index,1,{
        'id': this.id,
        'title': this.title,
        'completed': this.completed,
        'editing': this.editing,
      })
    },
    }
}
</script>
