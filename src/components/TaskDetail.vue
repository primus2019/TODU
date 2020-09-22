<template>
  <b-modal
    centered
    hide-backdrop
    :id="id"
    :header-bg-variant="darkMode ? 'dark' : 'light'"
    :body-bg-variant="darkMode ? 'dark' : 'light'"
    :footer-bg-variant="darkMode ? 'dark' : 'light'"
    @show="reset"
  >
    <template v-slot:modal-header style="align-items: center;">
      <h3
        v-if="!editTitle"
        :style="{ color: darkMode ? 'white' : 'black' }"
        @dblclick="editTitle = true"
      >{{ tmpTask.title }}</h3>
      <b-input-group-append
        v-if="editTitle"
      >
        <b-form-input
          autofocus
          v-model="titleOnInput"
          :style="{ color: darkMode ? 'white' : 'black', 'background-color': 'transparent' }"
          @keydown.enter="handleTitleInput"
          @blur="handleTitleInput"
        ></b-form-input>
      </b-input-group-append>
      <b-button-group class="ml-auto" v-if="!editDate && !editTime">
        <b-button :variant="importanceVariantMap[tmpTask.importance]" @click="editDate = true">
          <b-icon icon="calendar2-date"></b-icon>
          {{ tmpTask.date.substr(0, 4) + '-' + tmpTask.date.substr(4, 2) + '-' + tmpTask.date.substr(6, 2) }}
        </b-button>
        <b-button :variant="'outline-' + importanceVariantMap[tmpTask.importance]" @click="editTime = true">
          <b-icon icon="clock"></b-icon>
          {{ tmpTask.hour.toString().padStart(2, '0') + ' : ' + tmpTask.minute.toString().padStart(2, '0') }}
        </b-button>
      </b-button-group>
      <b-input-group-append
        v-if="editDate"
      >
        <b-form-input
          type="text"
          v-model="dateOnPick"
          placeholder="YYYY:MM:dd"
          @blur="handleDatePick"
          @keydown.enter="handleDatePick"
        ></b-form-input>
        <b-form-datepicker
          no-close-button
          button-only
          right
          hourCircle="h23"
          v-model="dateOnPick"
          :hour12="false"
          @hidden="handleDatePick"
        ></b-form-datepicker>
      </b-input-group-append>
      <b-input-group-append
        v-if="editTime"
      >
        <b-form-input
          type="text"
          v-model="timeOnPick"
          placeholder="HH:mm:ss"
          @blur="handleTimePick"
          @keydown.enter="handleTimePick"
        ></b-form-input>
        <b-form-timepicker
          no-close-button
          button-only
          right
          hourCircle="h23"
          v-model="timeOnPick"
          :hour12="false"
          @hidden="handleTimePick"
        ></b-form-timepicker>
      </b-input-group-append>
    </template>
    <template v-slot:modal-footer style="align-items: center; justify-coentents: end;">
      <!-- <b-button-group> -->
        <b-button
          block
          :variant="darkMode ? 'outline-light' : 'outline-dark'"
          @click="$bvModal.hide(id)"
        >
          cancel
        </b-button>
        <b-button
          block
          v-if="!addMode && task.title !== 'new task'"
          :variant="darkMode ? 'outline-light' : 'outline-dark'"
          @click="$emit('delete-task', tmpTask.task_id)"
        >
          archive
        </b-button>
        <b-button
          block
          v-if="!addMode"
          :variant="importanceVariantMap[tmpTask.importance]"
          @click="$emit('alter-task', tmpTask)"
        >
          {{ task.title === 'new task' ? 'add' : 'change'}}
        </b-button>
        <b-button
          block
          v-if="addMode"
          :variant="importanceVariantMap[tmpTask.importance]"
          @click="$emit('add-task', tmpTask)"
        >
          add
        </b-button>
      <!-- </b-button-group> -->
    </template>
    <b-textarea
      no-resize
      rows="8"
      :plaintext="!editDescription"
      v-model="tmpTask.description"
      :style="{ color: darkMode ? 'white' : 'black', 'background-color': 'transparent' }"
      @dblclick="editDescription = true"
      @keydown.ctrl.enter="editDescription = false"
      @blur="editDescription = false"
    >
    </b-textarea>
    <b-textarea
      no-resize
      :plaintext="!editLocation"
      v-model="tmpTask.location"
      :style="{ color: darkMode ? 'white' : 'black', 'background-color': 'transparent' }"
      @dblclick="editLocation = true"
      @keydown.ctrl.enter="editLocation = false"
      @blur="editLocation = false"
    >
    </b-textarea>
    <b-form-rating
      no-border
      show-clear
      icon-empty="star"
      stars="3"
      v-model="tmpTask.importance"
      :style="{ 'background-color': darkMode ? '#4d4d4d' : '#e9ecef' }"
      :color="importanceColorMap[tmpTask.importance]"
    ></b-form-rating>
  </b-modal>
</template>

<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
  name: 'TaskDetail',
  props: {
    task: Object,
    id: String,
    darkMode: Boolean,
    addMode: Boolean
  },
  data () {
    return {
      editDate: false,
      editTime: false,
      editTitle: false,
      editDescription: false,
      editLocation: false,
      editImportance: false,
      editDaily: false,
      tmpTask: { ...this.task },
      timeOnPick: '',
      dateOnPick: '',
      titleOnInput: '',
      importanceVariantMap: {
        0: 'primary',
        1: 'success',
        2: 'warning',
        3: 'danger'
      },
      importanceColorMap: {
        0: '#007bff',
        1: '#28a745',
        2: '#ff8800',
        3: '#dc3545'
      }
    }
  },
  methods: {
    reset (): void {
      this.editDate = false
      this.editTime = false
      this.editTitle = false
      this.editDescription = false
      this.editLocation = false
      this.editImportance = false
      this.editDaily = false
    },
    handleTimePick (): void {
      if (!this.timeOnPick) {
        this.editTime = false
      } else {
        this.tmpTask.hour = parseInt(this.timeOnPick.substring(0, 2))
        this.tmpTask.minute = parseInt(this.timeOnPick.substring(3, 5))
        this.editTime = false
      }
      this.timeOnPick = ''
    },
    handleDatePick (): void {
      if (!this.dateOnPick) {
        this.editDate = false
      } else {
        this.tmpTask.date = this.dateOnPick
        this.editTime = false
      }
      this.dateOnPick = ''
    },
    handleTitleInput (): void {
      if (this.titleOnInput) {
        this.tmpTask.title = this.titleOnInput
      }
      this.editTitle = false
      this.titleOnInput = ''
    }
  },
  watch: {
    task: {
      deep: true,
      handler: function (): void {
        this.tmpTask = { ...this.task }
      }
    }
  }
})
</script>

<style scoped>
</style>
