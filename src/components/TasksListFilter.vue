<template>
  <div>
    <div class="md-layout md-gutter">
      <div class="md-layout-item">
        <md-field>
          <label for="filter-input">Filter</label>
          <md-input
            id="filter-input"
            v-model="filter"
          />
        </md-field>
      </div>
      <div class="md-layout-item">
        <md-field>
          <label for="movie">Sorting by</label>
          <md-select v-model="sortBy">
            <md-option value="task">Task</md-option>
            <md-option value="user">User</md-option>
            <md-option value="time">Spent time</md-option>
            <md-option value="cost">Cost of work</md-option>
          </md-select>
        </md-field>
      </div>
      <div class="md-layout-item">
        <md-switch
          v-model="orderDesc"
          class="md-primary"
        >
          {{ orderDesc ? "Descending" : "Ascending" }}
        </md-switch>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "tasks-list-filter",
    data: () => ({
      orderDesc: false,
      sortBy: 'task',
      filter: ''
    }),
    watch: {
      orderDesc: function() {
        this.filterOrSortChange();
      },
      sortBy: function() {
        this.filterOrSortChange();
      },
      filter: function() {
        this.filterOrSortChange();
      },
    },
    methods: {
      filterOrSortChange: function() {
        this.$emit('filter-sort-change', {
          orderDesc: this.orderDesc,
          sortBy: this.sortBy,
          filter: this.filter
        })
      }
    }
  };
</script>