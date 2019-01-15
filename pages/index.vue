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
        input.checkbox-all(id="checkbox" type="checkbox" @click="toggleSelect()")

      .box(v-if="display === 'all'")
        .container-task(v-for="tasks in taskObj")
          input.checkbox-single(id="checkbox" type="checkbox" v-model="tasks.checked")
          p
            label(v-if="tasks.edit === false" v-on:dblclick="tasks.edit = true")
              | {{ tasks.data }}
            input.edit(v-if="tasks.edit === true" v-model="tasks.data"
              @keyup.enter="tasks.edit = false")
          button.remove-btn(@click="remove(tasks.id)")
            | x

      .box(v-else)
        .container-task(v-for="tasks in filtered")
          input.checkbox-single(id="checkbox" type="checkbox" v-model="tasks.checked")
          p
            label(v-if="tasks.edit === false"
                  v-on:dblclick="tasks.edit = true")
              | {{ tasks.data }}
            input.edit(v-if="tasks.edit === true" v-model="tasks.data"
                       @keyup.enter="tasks.edit = false")
          button.remove-btn(@click="remove(tasks.id)")
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
      button.remove-btn(@click="removeAll()")
        | Clear Selected
</template>

<script>
import taskPattern from '~/components/taskPattern.vue'

export default {
  data() {
    return {
      tasksObjOriginal: null,
      display: 'all',
      inputData: {
        id: null,
        checked: false,
        edit: false,
        data: null,
      },
      taskObj: [
        {
          id: 1,
          checked: false,
          edit: false,
          data: 'task one',
        },
        {
          id: 2,
          checked: false,
          edit: false,
          data: 'task two',
        },
        {
          id: 3,
          checked: false,
          edit: false,
          data: 'task three',
        },
        {
          id: 4,
          checked: false,
          edit: false,
          data: 'task four',
        }
      ],
      filtered: [],
    }
  },
  computed: {
    selectAll() {
      return this.taskObj.every((task) => task.checked);
    },
    //
    // TODO: try with computed filtered array ???
    //
    // filteredTasks() {
    //   // this.taskObj.some((task) => task.checked)
    //   if(this.display === 'done') {
    //     return this.taskObj.filter((task) => task.checked === true)
    //   } else if(this.display === 'active') {
    //     return this.taskObj.filter((task) => task.checked === false)
    //   }
    // },
    //
    checkedTasks() {
      return this.taskObj.filter((task) => task.checked === true);
    },
    itemsLeft() {
      return this.taskObj.length - this.checkedTasks.length;
    }
  },
  mounted() {
    this.arrLength();
  },
  methods: {
    addTask() {
      const clone = { ...this.inputData };

      this.inputData.id = this.tasksObjOriginal + 1;
      this.taskObj.push(clone);
      this.inputData.data = "";
      this.arrLength();
    },
    remove(id) {
      const index = this.taskObj.map(item => item.id).indexOf(id);
      this.taskObj.splice(index, 1);
    },
    removeAll() {
       this.taskObj = this.taskObj.filter(task => !this.checkedTasks
        .find(taskDone => task.id === taskDone.id));
    },
    toggleSelect() {
      let select = this.selectAll;
      this.taskObj.forEach((task) => task.checked = !select);
    },
    showAll() {
      this.display = 'all';
    },
    showDone() {
      this.display = 'done';
      this.filteredTasks();
    },
    showActive() {
      this.display = 'active';
      this.filteredTasks();
    },
    filteredTasks() {
      if(this.display === 'done') {
        this.filtered = this.taskObj.filter((task) => task.checked === true);
      } else if(this.display === 'active') {
        this.filtered = this.taskObj.filter((task) => task.checked === false);
      }
    },
    arrLength() {
      this.tasksObjOriginal = this.taskObj.length;
    },
  },
}
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
