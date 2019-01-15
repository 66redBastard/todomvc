<template lang="pug">
  section.container
    header.header
      h1.title todos
      form
        p
          input.header-input(placeholder="Wat needs to be done"
            v-model="inputData.data" @keydown.enter.prevent="addTask")
    section.main
      .toogle-all
        input.checkbox-all(id="checkbox" type="checkbox" @click="toggleIsDone()" :checked="togglerState")

      .box
        .container-task(v-for="task in shownTasks")
          input.checkbox-single(id="checkbox" type="checkbox" v-model="task.isDone")
          p
            label(v-if="task.edit === false" v-on:dblclick="task.edit = true" v-bind:class="{ 'done-task': task.isDone }")
              | {{ task.data }}
            input.edit(v-if="task.edit === true" v-model="task.data"
              @keyup.enter="task.edit = false")
          button.remove-btn(@click="remove(task.id)")
            | x

    footer.footer
      span {{ itemsLeft }} items left
      .container-btn
        button.show-btn(@click="showAll()")
          | All
        button.show-btn(@click="showActive()")
          | Active
        button.show-btn(@click="showDone()")
          | Done
      button.remove-btn(@click="removeDone()")
        | Clear Selected
</template>

<script>
import taskPattern from "~/components/taskPattern.vue";

export default {
  data() {
    return {
      // filter
      // values: all | done | active
      display: "all",

      // data for new task
      inputData: {
        id: null,
        isDone: false,
        edit: false,
        data: null
      },

      tasks: [
        {
          id: 1,
          isDone: false,
          edit: false,
          data: "task one"
        },
        {
          id: 2,
          isDone: false,
          edit: false,
          data: "task two"
        },
        {
          id: 3,
          isDone: false,
          edit: false,
          data: "task three"
        },
        {
          id: 4,
          isDone: false,
          edit: false,
          data: "task four"
        }
      ]
    };
  },
  computed: {
    doneTasks() {
      return this.tasks.filter(task => task.isDone === true);
    },
    activeTasks() {
      return this.tasks.filter(task => task.isDone === false);
    },

    shownTasks() {
      if (this.display === "all") {
        return this.tasks;
      } else if (this.display === "done") {
        return this.doneTasks;
      } else if (this.display === "active") {
        return this.activeTasks;
      }
    },

    newId() {
      const lastTaskIndex = this.tasks.length - 1;
      const lastTask = this.tasks[lastTaskIndex] || { id: 0 };
      return lastTask.id + 1;
    },

    itemsLeft() {
      return this.activeTasks.length;
    },
    togglerState() {
      return this.activeTasks.length === 0;
    }
  },
  methods: {
    addTask() {
      const clone = { ...this.inputData };

      this.inputData.id = this.newId;
      this.tasks.push(clone);
      this.inputData.data = "";
    },
    remove(id) {
      const index = this.tasks.map(item => item.id).indexOf(id);
      this.tasks.splice(index, 1);
    },
    removeDone() {
      this.tasks = this.tasks.filter(task => task.isDone === false);
    },
    showAll() {
      this.display = "all";
    },
    showDone() {
      this.display = "done";
    },
    showActive() {
      this.display = "active";
    },
    toggleIsDone() {
      let select = !this.togglerState;
      this.tasks.forEach(task => {
        task.isDone = select;
      });
    }
  }
};
</script>

<style lang="scss">
.header-input {
  width: 100%;
  padding: 10px 10px 10px 35px;
  outline: none;
}
.header,
.main,
.footer {
  position: relative;
  margin: 0 auto;
  width: 768px;
}
.toogle-all {
  width: 35px;
  position: absolute;
  top: -26px;
  z-index: 10;
}
.container-task {
  padding: 10px;
  border: 1px solid red;
  border-top: 0;
  margin: auto;
  position: relative;
  background-color: #fff;
  min-height: 40px;

  p {
    text-align: left;
    margin-left: 25px;
  }
}

.checkbox,
.checkbox-single,
.remove-btn {
  position: absolute;
  cursor: pointer;
}
.remove-btn {
  border: none;
  background-color: #fff;
  color: #fc4a4f;
  top: 0;
  right: 0;
  font-size: 20px;
}
.checkbox-all,
.checkbox-single {
  left: 10px;
  top: 50%;
  margin-top: -6px;
  z-index: 10;
}
.checkbox-single {
}
.done-task {
  text-decoration: line-through;
}
.footer {
  height: 50px;
  background-color: #fff;
  display: flex;
  justify-content: space-between;
  border: 1px solid #ccc;
  border-top: 0;
  span {
    padding-left: 15px;
    line-height: 50px;
  }
  .container-btn {
    line-height: 50px;
  }
  .show-btn {
    margin: 5px;
    outline: none;
    background-color: #fff;
  }
  .remove-btn {
    position: static;
    font-size: 16px;
  }
}
</style>
