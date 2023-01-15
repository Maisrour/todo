<template>
  <div class="container">
    <div class="row">
      <div class="col-12">
        <table class="table table-bordered mt-5">
          <thead>
            <tr>
              <th><span>To Do</span></th>
              <th><span>Actions</span></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in todolist" :key="index">
              <td class="align-middle w-75">
                {{ item.content }}
              </td>
              <td class="align-middle text-center w-75">
                <button class="btn btn-info btn-sm mx-1" @click="handelEdit(item.id)">
                  Edit
                </button>
                <button class="btn btn-danger btn-sm mx-1" @click="handelDelete(item.id)">
                  Delete
                </button>
              </td>
            </tr>
          </tbody>
          <tfoot>
            <td class="align-middle text-center w-75">
              <div class="form-group">
                <label for="">{{ editmode ? "Edit" : "Add" }}</label>
                <input type="text" class="form-control" v-model="todoitem.content" />
              </div>
            </td>
            <td class="align-middle text-center w-75">
              <button class="btn btn-primary btn-sm mx-1" @click="handeltodoitem">
                {{ editmode ? "Edit" : "Add" }}
              </button>
              <button
                class="btn btn-danger btn-sm mx-1"
                v-if="editmode"
                @click="handelCancel"
              >
                Cancel
              </button>
            </td>
          </tfoot>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import Axios from "axios";
const todoUrl = "http://localhost:3500/todo";

export default {
  data() {
    return {
      todolist: [],
      todoitem: {},
      editmode: false,
    };
  },
  methods: {
    handelEdit(id) {
      this.editmode = true;
      this.todoitem = this.todolist.find((item) => item.id == id);
    },
    handelCancel() {
      this.editmode = false;
      this.todoitem = "";
    },
    async handeltodoitem() {
      const id = this.todoitem.id;
      if (this.editmode) {
        await Axios.put(`${todoUrl}/${id}`, this.todoitem);
        this.editmode = false;
        this.todoitem.content = "";
      } else {
        await Axios.post(todoUrl, this.todoitem);
      }
      Axios.get(todoUrl).then((response) => (this.todolist = response.data));
    },
    async handelDelete(id) {
      await Axios.delete(`${todoUrl}/${id}`);
      Axios.get(todoUrl).then((response) => (this.todolist = response.data));
    },
  },
  created() {
    Axios.get(todoUrl).then((response) => (this.todolist = response.data));
  },
};
</script>

<style>
span {
  font-weight: 600;
}
table {
  width: 800px;
  border: 1px solid lightgray;
}

table th {
  padding: 10px;
  background-color: hsl(317deg 27% 75%);
}

table td {
  padding: 10px;
  background-color: hsl(337deg 61% 90%);
}

.btn.btn-info.btn-sm.mx-1 {
  background-color: #a9d3e1;
  border: none;
  margin-left: 10px;
  border-radius: 10px;
  padding: 6px;
}

.btn.btn-danger.btn-sm.mx-1 {
  background-color: #ff77bb;
  border: none;
  margin-left: 10px;
  border-radius: 10px;
  padding: 6px;
}

.btn.btn-primary.btn-sm.mx-1 {
  border: none;
  border-radius: 10px;
  padding: 6px;
  background-color: white;
}
</style>
