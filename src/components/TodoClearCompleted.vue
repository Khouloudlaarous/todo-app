<template>
  <button v-if="showClearCompletedButton" @click="clearCompleted">Clear completed</button>
</template>
<script>
export default {
  name: 'todo-clear-completed',
  computed: {
    showClearCompletedButton() {
     return this.$store.getters.showClearCompletedButton
    }
  },
  methods: {
    clearCompleted() {
      this.$store.dispatch('clearCompleted')
    }
  }
}
</script>
