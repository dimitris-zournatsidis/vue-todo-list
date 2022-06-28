<template>
  <div class="container">
    <div class="col-12">
      <table class="table table-bordered mt-5">
        <thead>
          <tr>
            <th>TODOS</th>
            <th class="text-center">Actions</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="item in todoList" :key="item.id">
            <td class="align-middle w-75">
              {{ item.content }}
            </td>
            <td class="align-middle text-center w-75">
              <button
                class="btn btn-info btn-sm mx-1"
                @click="handleEdit(item.id)"
              >
                Edit
              </button>
              <button
                class="btn btn-danger btn-sm mx-1"
                @click="handleDelete(item)"
              >
                Delete
              </button>
            </td>
          </tr>
        </tbody>

        <tfoot>
          <td class="align-middle text-center w-75">
            <div class="form-group">
              <label for="">{{ editMode ? "Edit" : "Add" }}</label>
              <input
                type="text"
                class="form-control"
                v-model="todoItem.content"
                placeholder="Add a todo..."
              />
            </div>
          </td>

          <td class="align-middle text-center w-25">
            <button
              class="btn bg-dark text-white btn-lg mx-1"
              @click="handleAddTodo"
            >
              {{ editMode ? "Edit" : "Add" }}
            </button>
            <button
              v-if="editMode"
              class="btn bg-danger text-white btn-lg mx-1"
              @click="handleCancel"
            >
              Cancel
            </button>
          </td>
        </tfoot>
      </table>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { useToast } from "vue-toastification";
import "vue-toastification/dist/index.css";
const todoUrl = "http://localhost:3500/todo";
const toast = useToast();

export default {
  data() {
    return {
      todoList: [],
      todoItem: {},
      editMode: false,
    };
  },
  methods: {
    async handleAddTodo() {
      if (!this.todoItem.content) {
        toast.error("Field must not be empty!");
      } else {
        const id = this.todoItem.id;
        if (this.editMode) {
          await axios.put(`${todoUrl}/${id}`, this.todoItem);
          this.editMode = false;
          this.todoItem.content = "";
        } else {
          await axios.post(todoUrl, this.todoItem);
          this.todoItem.content = "";
        }

        axios.get(todoUrl).then((response) => {
          this.todoList = response.data;
        });
      }

      // console.log("todos", this?.todoList);
    },
    handleEdit(id) {
      this.editMode = true;
      this.todoItem = this.todoList.find((item) => item.id === id);
    },
    handleCancel() {
      this.editMode = false;
      this.todoItem = "";
    },
    async handleDelete(todo) {
      if (window.confirm(`Do you really want to delete "${todo.content}"?`)) {
        await axios.delete(`${todoUrl}/${todo.id}`);
        axios.get(todoUrl).then((response) => {
          this.todoList = response.data;
        });
      }
    },
  },
  created() {
    axios.get(todoUrl).then((response) => {
      //   console.log('res!!!', response);
      this.todoList = response.data;
    });
  },
};
</script>

<style>
@import "~bootstrap/dist/css/bootstrap.css";
</style>
