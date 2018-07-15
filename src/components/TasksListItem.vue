<template>
  <md-list-item class="list-item">
    <p class="list-item__name-col">
      <span class="md-body-2">Task: {{ task.name }}</span><br>
      <span class="md-caption">User: {{ user.name }}</span>
    </p>
    <p class="list-item__name-col">
      <span class="md-body-1">Spent time: {{ task.time }}h</span><br>
      <span class="md-body-1">Cost of work: {{ task.cost }}</span>
    </p>
    <div>
      <md-button
        class="md-icon-button"
        @click="$emit('open-edit-dialog', task.id)"
      >
        <md-icon>edit</md-icon>
      </md-button>
      <md-button
        class="md-icon-button"
        @click="$emit('remove-task', task.id)"
      >
        <md-icon>delete</md-icon>
      </md-button>
    </div>
  </md-list-item >
</template>

<script>
  import { MdSelect, MdOption, MdListItem } from 'vue-material/dist/components'
  import { keyBy } from 'lodash'

  export default {
    name: "tasks-list-item",
    props: {
      task: Object,
      user: Object
    },
  };
</script>

<style>
</style>
