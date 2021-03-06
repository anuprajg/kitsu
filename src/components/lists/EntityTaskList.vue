<template>
<div class="data-list">

  <div style="overflow: hidden">
    <table class="datatable" ref="headerWrapper">
      <thead class="datatable-head">
        <tr class="datatable-row-header">
          <th class="type">
            {{ $t('tasks.fields.task_type') }}
          </th>
          <th class="status">
            {{ $t('tasks.fields.task_status') }}
          </th>
          <th class="assignees">
            {{ $t('tasks.fields.assignees') }}
          </th>
          <th class="end-cell"></th>
        </tr>
      </thead>
    </table>
  </div>

  <table-info
    :is-loading="isLoading"
    :is-error="isError"
  />

  <div v-scroll="onBodyScroll" v-if="entries.length > 0">
    <table class="datatable">
      <tbody class="datatable-body">
        <tr
          :key="taskId"
          :class="{
            selected: currentTask && currentTask.id === taskId,
            'datatable-row': true,
            'datatable-row--selectable': true
          }"
          @click="selectTask(getTask(taskId))"
          v-for="taskId in entries"
        >
          <task-type-cell
            class="type"
            :task-type="getTaskType(taskId)"
            :production-id="currentProduction.id"
            v-if="getTaskType(taskId)"
          />
          <td class="status">
            <validation-tag
              :task="getTask(taskId)"
              :is-static="true"
              v-if="getTask(taskId)"
            />
          </td>
          <td class="assignees">
            <div class="flexrow">
              <div
                class="avatar-wrapper"
                :key="personId"
                v-for="personId in getAssignees(taskId)"
              >
                <people-avatar
                  class="person-avatar flexrow-item"
                  :key="taskId + '-' + personId"
                  :person="personMap[personId]"
                  :size="30"
                  :font-size="15"
                />
              </div>
            </div>
          </td>
          <td class="end-cell"></td>
       </tr>
      </tbody>
    </table>
  </div>

</div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'

import TaskTypeCell from '../cells/TaskTypeName'
import TableInfo from '../widgets/TableInfo'
import ValidationTag from '../widgets/ValidationTag'
import PeopleAvatar from '../widgets/PeopleAvatar'

export default {
  name: 'entity-task-list',

  components: {
    TableInfo,
    TaskTypeCell,
    PeopleAvatar,
    ValidationTag
  },

  props: {
    entries: {
      type: Array,
      default: () => []
    },
    isLoading: {
      type: Boolean,
      default: false
    },
    isError: {
      type: Boolean,
      default: false
    }
  },

  data () {
    return {
      currentTask: null
    }
  },

  computed: {
    ...mapGetters([
      'currentProduction',
      'personMap',
      'taskMap',
      'taskTypeMap'
    ])
  },

  methods: {
    ...mapActions([
    ]),

    onBodyScroll (event, position) {
      this.$refs.headerWrapper.style.left = `-${position.scrollLeft}px`
    },

    getTask (task) {
      if (typeof (task) === 'string') {
        return this.taskMap[task]
      } else {
        return task
      }
    },

    getTaskType (entry) {
      const task = this.getTask(entry)
      return task ? this.taskTypeMap[task.task_type_id] : null
    },

    getAssignees (entry) {
      const task = this.getTask(entry)
      return task ? task.assignees : []
    },

    selectTask (task) {
      this.currentTask = task
      this.$emit('task-selected', task)
    }
  }
}
</script>

<style lang="scss" scoped>
.data-list {
  max-width: 500px;
  margin-top: 0;
}

.type {
  max-width: 250px;
  min-width: 250px;
}

.status {
  max-width: 100px;
  min-width: 100px;
}

.assignees {
  max-width: 150px;
  min-width: 150px;
}

.end-cell {
  width: 100%;
}

.flexrow-item {
  margin-right: 0.3em;
}

.avatar-wrapper {
  margin-right: 0.5em;
}
</style>
