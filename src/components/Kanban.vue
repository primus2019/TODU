<template>
  <b-row style="margin: 0; padding: 0;">
    <b-col
      xl="2" lg="2" md="2" sm="2" cols="2"
      v-for="(listHeader, listIndex) in listHeaders"
      :key="listIndex"
    >
      <h3
        :style="{ color: darkMode ? 'white' : 'black', 'background-color': 'transparent', display: 'inline' }"
        @click="$emit('change-title', listHeader.list_id)"
      >
        {{ listHeader.list_title }}
      </h3>
      <draggable
        class="list-group"
        group="people"
        v-if="!Object.keys(localTaskLists).includes(listHeader.list_id.toString())"
        :list="Object.keys(localTaskLists).includes(listHeader.list_id.toString()) ? localTaskLists[listHeader.list_id].tasks : null"
        @add="(evt) => handleAdd(listHeader.list_id, evt)"
        @remove="(evt) => handleRemove(listHeader.list_id, evt)"
        @change="log"
      ></draggable>
      <draggable
        class="list-group"
        group="people"
        v-if="Object.keys(localTaskLists).includes(listHeader.list_id.toString())"
        :list="Object.keys(localTaskLists).includes(listHeader.list_id.toString()) ? localTaskLists[listHeader.list_id].tasks : null"
        @add="(evt) => handleAdd(listHeader.list_id, evt)"
        @remove="(evt) => handleRemove(listHeader.list_id, evt)"
        @change="log"
      >
        <b-button
          squared
          v-for="(task, taskIndex) in localTaskLists[listHeader.list_id].tasks"
          class="hvr-bounce-to-right"
          :style="{ '--height': '45pt', '--test': importanceColorMap[task.importance] }"
          :variant="darkMode ? 'dark' : 'light'"
          :key="taskIndex"
          @click="$emit('show-task-detail', listHeader.list_id, taskIndex)"
        >
          {{ task.title }}
        </b-button>
      </draggable>
      <div class="list-group">
        <b-button
          squared
          class="hvr-fade"
          :style="{ '--color': importanceColorMap[3] }"
          :draggable="false"
          :variant="darkMode ? 'dark' : 'light'"
          @click="$emit('delete-list', listHeader.list_id)"
        >
          <b-icon icon="bookmark-dash"></b-icon>
        </b-button>
      </div>
      <b-button
        id="handle-task-button"
        :variant="darkMode ? 'dark' : 'light'"
        @click="$emit('add-task', listHeader.list_id)"
      >
        <b-icon icon="node-plus" rotate="270"></b-icon>
      </b-button>
    </b-col>
  </b-row>
</template>

<script lang="ts">
import Vue from 'vue'
import draggable from 'vuedraggable'
export default Vue.extend({
  name: 'Kanban',
  components: {
    draggable
  },
  props: {
    taskLists: Object,
    darkMode: Boolean,
    listHeaders: Array
  },
  data () {
    return {
      editStartTime: false,
      editEndTime: false,
      editTitle: false,
      editDescription: false,
      editLocation: false,
      editImportance: false,
      editDaily: false,
      titleOnInput: '',
      importanceColorMap: {
        0: '#007bff',
        1: '#28a745',
        2: '#ffc107',
        3: '#c82333'
      },
      localTaskLists: { ...this.taskLists },
      moveListId: 0,
      moveTaskId: 0
    }
  },
  methods: {
    handleAdd (listIndex: number, evt: Record<string, any>) {
      console.log('handleAdd', listIndex, evt.oldIndex, evt.newIndex)
      this.moveListId = listIndex
      console.log('move', this.moveListId, this.moveTaskId)
    },
    handleRemove (listIndex: number, evt: Record<string, any>) {
      console.log('handleRemove', listIndex, evt.oldIndex, evt.newIndex)
      this.moveTaskId = this.taskLists[listIndex].tasks[evt.oldIndex].task_id
      this.$emit('move', this.moveListId, this.moveTaskId)
    },
    handleChange (listId: number, evt: Record<string, any>) {
      console.log('handleChange', evt.oldIndex, evt.newIndex)
    },
    log (evt: Event) {
      window.console.log(evt)
    }
  },
  watch: {
    taskLists: {
      deep: true,
      handler: function (): void {
        this.localTaskLists = this.taskLists
      }
    }
  }
})
</script>

<style scoped>
.hvr-bounce-to-right {
  height: var(--height);
}
.hvr-bounce-to-right:before {
  background: var(--test);
}
#handle-task-button  {
  width: 30pt;
  height: 30pt;
  border-radius: 30pt;
  padding: 0;
  vertical-align: text-top;
}
.hvr-fade:hover, .hvr-fade:focus, .hvr-fade:active {
  background-color: var(--color);
  color: white;
}
</style>
