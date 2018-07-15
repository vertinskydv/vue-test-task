<template>
  <div>
    <span class="md-body-2">
      Total time: {{ totalSpentTime }}h<br>
      Total cost of work: {{ totalCostOfWork }}
    </span>
  </div>
</template>

<script>
  export default {
    name: "tasks-list-total",
    props: {
      tasks: Array
    },
    computed: {
      totalSpentTime() {
        return this.tasks.reduce((total, task) => {
          return total + task.time;
        }, 0)
      },
      totalCostOfWork() {
        return this.tasks.reduce((total, task) => {
          return total + task.cost;
        }, 0)
      }
    }
  };
</script>