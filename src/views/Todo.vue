<template>
  <b-container
    :style="{ 'background-color': darkMode ? '#191b1c' : '#ffffff', padding: '0px', height: '100vh' }"
    :fluid="true"
  >
    <b-row style="margin: 0; padding: 0;">
      <Navbar
        :darkMode="darkMode"
        @shift-dark-mode="handleShiftDarkMode"
        @add-list="handleAddList"
      ></Navbar>
    </b-row>
    <Kanban
      :darkMode="darkMode"
      :listHeaders="listHeaders"
      :taskLists="taskLists"
      @show-task-detail="handleShowTaskDetail"
      @change-title="handleChangeTitle"
      @add-task="handleAddTask"
      @delete-list="handleDeleteList"
      @move="handleTaskMove"
      @change-list-rank="handleChangeListRank"
    ></Kanban>
    <TaskDetail
      id="task-detail"
      :task="taskOnShow"
      :darkMode="darkMode"
      :addMode="addMode"
      @delete-task="handleDeleteTask"
      @alter-task="handleAlterTask"
      @add-task="handleSetTask"
    ></TaskDetail>
    <TitleInput
      id="title-input"
      :darkMode="darkMode"
      :addMode="addMode"
      @set-new-title="handleSetNewTitle"
      @set-new-list-title="handleSetNewList"
    ></TitleInput>
  </b-container>
</template>

<script lang="ts">
import Vue from 'vue'
import axios from 'axios'
import Kanban from '../components/Kanban.vue'
import Navbar from '../components/Navbar.vue'
import TaskDetail from '../components/TaskDetail.vue'
import TitleInput from '../components/TitleInput.vue'

export default Vue.extend({
  name: 'Todo',
  components: {
    Kanban,
    Navbar,
    TaskDetail,
    TitleInput
  },
  data () {
    return {
      taskLists: {},
      listHeaders: [],
      darkMode: false,
      addMode: false,
      taskOnShow: {},
      taskOnHandle: 0,
      listOnHandle: 0,
      domain: ''
    }
  },
  methods: {
    handleShiftDarkMode (): void {
      this.darkMode = !this.darkMode
    },
    handleShowTaskDetail (listIndex: number, taskIndex: number): void {
      console.log('handleShowTaskDetail', listIndex, taskIndex)
      this.addMode = false
      this.taskOnShow = (this.taskLists as Record<string, any>)[listIndex].tasks[taskIndex]
      this.listOnHandle = listIndex
      this.taskOnHandle = (this.taskLists as Record<string, any>)[listIndex].tasks[taskIndex].task_id
      this.$bvModal.show('task-detail')
    },
    handleChangeTitle (listIndex: number): void {
      console.log('handleChangeTitle', listIndex)
      this.listOnHandle = listIndex
      this.addMode = false
      this.$bvModal.show('title-input')
    },
    handleSetNewTitle (newTitle: string): void {
      console.log('handleSetNewTitle', newTitle, this.listOnHandle)
      axios({
        method: 'post',
        url: 'http://' + this.domain + ':2000/todu/alter_title',
        data: {
          listId: this.listOnHandle,
          title: newTitle
        }
      })
        .then((res) => {
          if (res.data.stats === 0) {
            console.log('alter title set')
          } else {
            console.log('alter title fails')
          }
          this.reviewLists()
          this.reviewTaskLists()
        })
        .catch((err) => { console.log(err) })
    },
    handleDeleteTask (): void {
      console.log('handleDeleteTask', this.taskOnHandle)
      axios({
        method: 'post',
        url: 'http://' + this.domain + ':2000/todu/delete_task',
        data: {
          taskId: this.taskOnHandle
        }
      })
        .then((res) => {
          if (res.data.status === 0) {
            console.log('delete task set')
          } else {
            console.log('delete task fails')
          }
          this.reviewLists()
          this.reviewTaskLists()
        })
    },
    handleAlterTask (task: Record<string, any>): void {
      axios({
        method: 'post',
        url: 'http://' + this.domain + ':2000/todu/alter_task',
        data: {
          taskId: this.taskOnHandle,
          task: task
        }
      })
        .then((res) => {
          if (res.data.status === 0) {
            console.log('alter task set')
          } else {
            console.log('alter task fails')
          }
          this.reviewLists()
          this.reviewTaskLists()
        })
        .catch((err) => { console.log(err) })
    },
    handleAddTask (listIndex: number): void {
      console.log('handleAddTask')
      this.listOnHandle = listIndex
      this.addMode = true
      this.taskOnShow = {
        date: this.getTomorrowDate(),
        hour: 0,
        minute: 0,
        title: 'new title',
        description: null,
        location: null,
        importance: 0,
        daily: 0
      }
      this.$bvModal.show('task-detail')
    },
    // from the node button
    handleSetTask (task: Record<string, any>): void {
      console.log('handleSetTask')
      axios({
        method: 'post',
        url: 'http://' + this.domain + ':2000/todu/add_task',
        data: {
          listId: this.listOnHandle,
          task: task
        }
      })
        .then((res) => {
          console.log('POST: handleSetTask response.data', res.data)
          if (res.data.status === 0) {
            console.log('add task set')
          } else {
            console.log('add task fails')
          }
          this.reviewLists()
          this.reviewTaskLists()
        })
        .catch((err) => { console.log(err) })
    },
    handleAddList (): void {
      console.log('handleAddList')
      this.addMode = true
      this.$bvModal.show('title-input')
    },
    handleSetNewList (newTitle: string): void {
      console.log('handleSetNewList', newTitle)
      axios({
        method: 'post',
        url: 'http://' + this.domain + ':2000/todu/add_list',
        data: { title: newTitle }
      })
        .then((res) => {
          console.log('POST: add_list response.data', res.data)
          if (res.data.status === 0) {
            console.log('new title set')
          } else {
            console.log('new title fails')
          }
          this.reviewLists()
          this.reviewTaskLists()
        })
    },
    handleDeleteList (listIndex: number): void {
      console.log('POST: handleDeleteList', listIndex)
      axios({
        method: 'post',
        url: 'http://' + this.domain + ':2000/todu/delete_list',
        data: { listIndex: listIndex }
      })
        .then((res) => {
          console.log('POST: handleDeleteList response.data', res.data)
          if (res.data.status === 0) {
            console.log('delete list set')
          } else {
            console.log('delete list fails')
          }
          this.reviewLists()
          this.reviewTaskLists()
        })
        .catch((err) => {
          console.log(err)
        })
    },
    handleTaskMove (listId: number, taskId: number): void {
      console.log('handleTaskMove', {
        method: 'post',
        url: 'http://' + this.domain + ':2000/todu/move_tasks',
        data: {
          taskId: taskId,
          listId: listId
        }
      })
      axios({
        method: 'post',
        url: 'http://' + this.domain + ':2000/todu/move_tasks',
        data: {
          taskId: taskId,
          listId: listId
        }
      })
        .then((res) => {
          if (res.data.status === 0) {
            console.log('move tasks set')
          } else {
            console.log('move tasks fails')
          }
          this.reviewLists()
          this.reviewTaskLists()
        })
        .catch((err) => { console.log(err) })
    },
    handleChangeListRank (listIndex: number): void {
      console.log('handleChangeListRank')
    },
    reviewLists (): void {
      axios({
        method: 'get',
        url: 'http://' + this.domain + ':2000/todu/review_lists'
      })
        .then((res) => {
          if (res.data.status === 0) {
            console.log('review lists set', res.data)
            this.listHeaders = res.data.listHeaders
          } else {
            console.log('review lists fails')
          }
        })
        .catch((err) => { console.log(err) })
    },
    reviewTaskLists (): void {
      console.log('reviewTaskLists')
      axios({
        method: 'get',
        url: 'http://' + this.domain + ':2000/todu/review_tasklists'
      })
        .then((res) => {
          console.log('review_tasklists: response.data', res.data)
          this.taskLists = res.data
        })
        .catch((err) => { console.log(err) })
    },
    getTomorrowDate (): string {
      const tmpDate = new Date()
      tmpDate.setDate(tmpDate.getDate() + 1)
      return tmpDate.getFullYear().toString() + (tmpDate.getMonth() + 1).toString().padStart(2, '0') + tmpDate.getDate().toString().padStart(2, '0')
    },
    setDomain (): void {
      if (!process.env.NODE_ENV || process.env.NODE_ENV === 'development') {
        this.domain = 'localhost'
      } else {
        this.domain = '172.81.225.58'
      }
    }
  },
  mounted (): void {
    this.setDomain()
    this.reviewLists()
    this.reviewTaskLists()
  }
})
</script>
