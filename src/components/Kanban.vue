<template>
  <b-row style="margin: 0; padding: 0;">
    <b-col
      xl="2" lg="2" md="2" sm="2" cols="2"
      v-for="(list, listIndex) in taskLists"
      :key="listIndex"
    >
      <h3
        :style="{ color: darkMode ? 'white' : 'black', 'background-color': 'transparent', display: 'inline' }"
        @click="$emit('change-title', listIndex)"
      >
        {{ list.list_title }}
      </h3>
      <draggable
        class="list-group"
        group="people"
        :list="list.tasks"
        @add="(evt) => log(listIndex, evt.newIndex)"
      >
        <b-button
          squared
          v-for="(task, taskIndex) in list.tasks"
          class="hvr-bounce-to-right"
          :style="{ '--height': '45pt', '--test': importanceColorMap[task.importance] }"
          :variant="darkMode ? 'dark' : 'light'"
          :key="taskIndex"
          @click="$emit('show-task-detail', listIndex, taskIndex)"
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
          @click="$emit('delete-list', listIndex)"
        >
          <b-icon icon="bookmark-dash"></b-icon>
        </b-button>
      </div>
      <b-button
        id="handle-task-button"
        :variant="darkMode ? 'dark' : 'light'"
        @click="$emit('add-task', listIndex)"
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
    darkMode: Boolean
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
      }
    }
  },
  methods: {
    log (listIndex: number, taskIndex: number) {
      this.$emit('move', listIndex, taskIndex)
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
