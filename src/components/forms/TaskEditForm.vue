<template>
  <div>
    <md-field md-dynamic-height>
      <label>Task name</label>
      <md-input v-model="task.name" />
    </md-field>
    <md-field>
      <label>User</label>
      <md-select v-model="task.user_id">
        <md-option
          v-for="user in usersById"
          :key="user.id"
          :value="user.id"
        >
          {{ user.name }}
        </md-option>
      </md-select>
    </md-field>
    <md-field md-dynamic-height>
      <label>Spent time</label>
      <md-input
        v-model.number="task.time"
        type="number"
      />
    </md-field>
    <span class="md-subheading">Cost of work: {{ task.cost }}</span>
  </div>
</template>

<script>
  export default {
    name: "task-edit-form",
    props: {
      usersById: Object,
      task: Object
    },
    watch: {
      "task.user_id": function() {
        this.recountCost();
      },
      "task.time": function() {
        this.recountCost();
      },
    },
    methods: {
      recountCost: function() {
        this.task.cost = this.task.time * this.usersById[this.task.user_id].price;
      }
    }
  };
</script>

<style>
</style>
