<template>
  <div class="container">
    <h2>Tasks manager</h2>
    <tasks-list-filter
      v-on:filter-sort-change="filterOrSortChange"
    />
    <md-list>
      <template v-for="(task, index) in filteredTasks">
        <tasks-list-item
          v-bind:key="task.id"
          v-bind:task="task"
          v-bind:user="usersById[task.user_id]"
          v-on:remove-task="onOpenConfirmRemoveDialog"
          v-on:open-edit-dialog="openEditDialog"
        />
        <md-divider v-bind:key="task.id + 'divider'" v-if="tasks.length - 1 !== index"></md-divider>
      </template> 
    </md-list>
    <div class="md-layout list-bottom-bar">
      <div class="md-layout-item">
        <tasks-list-total
          v-bind:tasks="filteredTasks"
        />
      </div>
      <div class="md-layout-item md-layout md-size-50 md-alignment-center-right">
        <md-button
          class="md-layout-item md-raised"
          v-bind:disabled="disableCahngeControls"
          @click="onCancelChanges"
        >
          Сancel changes
        </md-button>
        <md-button
          class="md-layout-item md-raised md-primary"
          v-bind:disabled="disableCahngeControls"
          @click="onSaveChanges"
        >
          Save changes
        </md-button>
      </div>
    </div>
    <md-dialog :md-active.sync="edit.editDialogOpen">
      <md-dialog-title>Edit task</md-dialog-title>
      <md-dialog-content>
        <task-edit-form
          v-bind:task="edit.editableTask"
          v-bind:usersById="usersById"
        >
        </task-edit-form>
      </md-dialog-content>
      <md-dialog-actions>
        <md-button class="md-primary" @click="closeEditDialog">Close</md-button>
        <md-button class="md-primary" @click="okEditDialog">Ok</md-button>
      </md-dialog-actions>
    </md-dialog>
    <md-dialog-confirm
      :md-active.sync="del.confirmDeleteDialogOpen"
      md-title="Confirm that you want to delete the task."
      md-confirm-text="Delete"
      md-cancel-text="Cancel"
      @md-cancel="onCancelConfirmRemoveDialog"
      @md-confirm="removeTask"
    />
    <md-snackbar md-position="center" :md-duration="4000" :md-active.sync="showSnackbar">
      <span>Fake server request! Check request data in console.</span>
      <md-button class="md-primary" @click="showSnackbar = false">Retry</md-button>
    </md-snackbar>
  </div>
</template>

<script>

import TasksListItem from './components/TasksListItem';
import TasksListFilter from './components/TasksListFilter';
import TasksListTotal from './components/TasksListTotal';
import TaskEditForm from './components/forms/TaskEditForm';
import { MdList, MdListItem } from 'vue-material/dist/components';
import { keyBy, cloneDeep, isEqual } from 'lodash';

export default {
  name: 'app',

  components: {
    TasksListItem,
    TasksListFilter,
    TaskEditForm,
    TasksListTotal
  },

  data () {
    return {
      users: [],
      tasks: [],
      orderDesc: false,
      sortBy: 'task',
      filter: '',
      showSnackbar: false,
      del: {
        deleteId: null,
        confirmDeleteDialogOpen: false,
      },
      edit: {
        editDialogOpen: false,
        editableTask: null,
        editedTasks: {}
      }
    }
  },

  methods: {
    onOpenConfirmRemoveDialog: function(id) {
      this.del.deleteId = id;
      this.del.confirmDeleteDialogOpen = true;
    },
    removeTask: function() {
      const taskId = this.del.deleteId;

      // in this place there must be a real request to the server
      console.log("In this case, the data is sent in the URL");
      this.showSnackbar = true;

      const index = this.tasks.findIndex((task) => {
        return taskId === task.id
      });
      if (index !== -1) {
        this.$delete(this.tasks, index);
      }
      this.del.deleteId = null;
    },
    onCancelConfirmRemoveDialog: function() {
      this.del.deleteId = null;
      this.del.confirmDeleteDialogOpen = false;
    },
    onCancelChanges: function() {
      this.$set(this.edit, "editedTasks", {});
    },
    onSaveChanges: function() {
      // in this place there must be a real request to the server
      console.log("In this case, the data is sent in the URL");
      this.showSnackbar = true;
      console.log("Edit task:");
      console.log(JSON.parse(JSON.stringify(Object.values(this.edit.editedTasks))));

      this.tasks.forEach((task, index) => {
        if (this.edit.editedTasks[task.id]) {
          this.$set(this.tasks, index, this.edit.editedTasks[task.id]);
        }
      });
      this.$set(this.edit, "editedTasks", {});
    },
    openEditDialog: function(taskId) {
      this.edit.editDialogOpen = true;
      const taskIndex = this.filteredTasks.findIndex((task) => {
        return taskId === task.id
      });
      this.$set(this.edit, "editableTask", JSON.parse(JSON.stringify(this.filteredTasks[taskIndex])));
    },
    closeEditDialog: function() {
      this.edit.editDialogOpen = false;
      this.edit.editableTask = null;
    },
    okEditDialog: function() {
      const editedTask = this.edit.editableTask;
      const originalTask = this.tasks.find((task) => {
        return editedTask.id === task.id;
      });
      if (!isEqual(editedTask, originalTask)) {
        this.$set(this.edit.editedTasks, editedTask.id, editedTask);
      }
      else {
        this.$delete(this.edit.editedTasks, editedTask.id);
      }
      this.edit.editDialogOpen = false;
      this.edit.editableTask = null;
    },
    filterOrSortChange(data) {
      this.orderDesc = data.orderDesc;
      this.sortBy = data.sortBy;
      this.filter = data.filter;
    }
  },

  computed: {
    usersById: function() {
      return keyBy(this.users, (user) => user.id);
    },
    tasksById: function() {
      return keyBy(this.users, (user) => user.id);
    },
    disableCahngeControls: function() {
      return !Object.values(this.edit.editedTasks).length;
    },
    filteredTasks: function() {
      const editedTasks = this.edit.editedTasks;
      let resultTasks = this.tasks.map((task) => {
        if (editedTasks[task.id]) {
          return editedTasks[task.id];
        }
        return task;
      });

      if (this.filter) {
        const filter = this.filter.toLowerCase();
        resultTasks = resultTasks.filter((task) => {
          const taskUser = this.usersById[task.user_id];
          return task.name.toLowerCase().includes(filter) ||
            taskUser.name.toLowerCase().includes(filter) ||
            task.cost.toString().includes(filter) ||
            task.time.toString().includes(filter);
        });
      }

      function ascCompare(a, b) {
        if(a < b) return -1;
        if(a > b) return 1;
        return 0;
      }

      function descCompare(a, b) {
        if(a > b) return -1;
        if(a < b) return 1;
        return 0;
      }

      return resultTasks.sort((a, b) => {
        const aUser = this.usersById[a.user_id];
        const bUser = this.usersById[b.user_id];
        switch (this.sortBy) {
          case "task":
            return this.orderDesc ? descCompare(a.name, b.name) : ascCompare(a.name, b.name);
            break;
          case "user":
            return this.orderDesc ? descCompare(aUser.name, bUser.name) : ascCompare(aUser.name, bUser.name);
            break;
          case "time":
            return this.orderDesc ? descCompare(a.time, b.time) : ascCompare(a.time, b.time);
            break;
          case "cost":
            return this.orderDesc ? descCompare(a.cost, b.cost) : ascCompare(a.cost, b.cost);
            break;
        }
      });
    }
  },

  watch: {
    tasks: function() {
      this.tasks.forEach((task) => {
        const user = this.usersById[task.user_id];
        task.cost = task.time * user.price;
      });
    }
  },

  mounted () {
    fetch("data.json").then(
      (data) => {
        if (data.status !== 200) {
          throw new Error('Fetch initial data error')
        }
        return data.json();
      }
    ).then (
      (data) => {
        this.users = data.users;
        this.tasks = data.tasks;
      }
    )
  }
}
</script>

<style>
body {
  margin: 0;
}

.container {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  width: 900px;
  margin: 0 auto;
}

.list-bottom-bar {
  margin-top: 20px;
}
</style>
