<template>
  <q-page class="bg-grey-3 column">
    <div class="row q-pa-sm bg-primary">
      <q-input
        filled
        bg-color="white"
        class="col"
        v-model="newTask"
        @keyup.enter="addTask"
        label="Add a task"
      >
        <template v-slot:append>
          <q-btn @click="addTask" round dense flat icon="add" />
        </template>
      </q-input>
      <div v-if="tasks.length > 1" class="items-end q-pl-sm q-mb-sm">
        <q-btn
          unelevated
          stack
          icon="delete"
          color="negative"
          label="Clear all tasks"
          @click="openDialogBox(emptyTaskArray)"
        />
      </div>
    </div>

    <q-list class="bg-white" seperator bordered>
      <q-item
        v-for="(task, index) in tasks"
        :key="task.title"
        @click="task.status.value = setTaskStatus(task.status)"
        :class="{
          'status-done bg-blue-2': task.status.value === true,
          'bg-red-2': task.status.value === null,
          'bg-white': task.status.value === false,
        }"
        clickable
        v-ripple
      >
        <q-item-section avatar>
          <q-checkbox
            class="no-pointer-events"
            v-model="task.status.value"
            color="primary"
          />
        </q-item-section>
        <q-item-section>
          <q-item-label>{{ task.title }}</q-item-label>
          <q-item-label caption>{{ getTaskCaption(task.status) }}</q-item-label>
        </q-item-section>
        <q-item-section v-if="task.status.value" side>
          <q-btn
            @click.stop="openDialogBox(deleteTask, index)"
            flat
            round
            dense
            color="primary"
            icon="delete"
          />
        </q-item-section>
      </q-item>
    </q-list>

    <div v-if="!tasks.length" class="no-tasks absolute-center">
      <q-icon name="check" size="100px" color="primary" />
      <div class="text-h5 text-primary text-center">No tasks</div>
    </div>
  </q-page>
</template>

<script>
const taskCaption = {
  dn: "Done",
  na: "Not Attempted",
  inc: "Incomplete",
};
import { getCurrentInstance, defineComponent, ref } from "vue";
import { useQuasar } from "quasar";

export default defineComponent({
  name: "Todo",

  setup() {
    const $q = useQuasar();
    const currentInstance = getCurrentInstance();
    function getTaskCaption(status) {
      let caption =
        status.value === null
          ? taskCaption.inc
          : status.value === false
          ? taskCaption.na
          : taskCaption.dn;
      return caption;
    }

    function setTaskStatus(status) {
      // console.log({ previousStatus: status.value });
      let newStatus =
        status.value === false ? null : status.value === null ? true : false;
      // console.log({ currentStatus: newStatus });
      return newStatus;
    }

    function notification(message, color = "green") {
      $q.notify({ message: message, color: color });
    }

    function openDialogBox(cb, param = null) {
      $q.dialog({
        title: "Confirm",
        message: "Are you sure you want to delete the task?",
        cancel: true,
        persistent: true,
      }).onOk(() => {
        if (param !== null) cb(param);
        else cb();
      });
    }

    return {
      openDialogBox,
      setTaskStatus,
      notification,
      getTaskCaption,
      newTask: ref(""),
      tasks: [],
    };
  },

  methods: {
    deleteTask(index) {
      /**
       The .stop modifier (used with the @click) makes it possible to click on
      the delete icon since the main q-item has already
      been made clickable
      **/
      this.tasks.splice(index, 1);
      this.notification("Task deleted! Click again to remove.", "purple");
    },
    addTask() {
      if (this.newTask === "") return null;
      this.tasks.push({
        title: this.newTask,
        status: ref(false),
      });
      this.newTask = "";
      this.notification("Task added!");
    },
    emptyTaskArray() {
      this.tasks.length = 0;
      // this.tasks = [];
    },
  },
});
</script>

<style lang="scss">
.status-done {
  .q-item__label {
    text-decoration: line-through;
    color: #949191;
  }

  .text-caption {
    text-decoration: none;
  }
}

.no-tasks {
  opacity: 0.5;
}

.q-btn--rectangle {
  border-radius: 6px;
}
</style>
