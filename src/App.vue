<template>
  <div>
    <div class="container mt-4">
      <div class="row">
        <div class="col-12">
          <button type="button" class="btn btn-success mb-3" @click="create">Add</button>

          <ul id="todo-list" class="list-group">
            <li  v-for="todoItem in todoList" class="list-group-item d-flex justify-content-between align-items-center">
              <div>
                <input class="form-check-input" type="checkbox" id="termsCheck" @click="toggleCompleted(todoItem)">
                {{ todoItem.title }}
              </div>
              <div><small>{{ todoItem.description }}</small></div>

              <div>
                <button class="btn btn-warning btn-sm" @click="edit(todoItem.uuid)">Edit</button>
                <button class="btn btn-danger btn-sm" @click="confirmRemove(todoItem.uuid)">Remove</button>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>

    <div id="form-modal" class="modal d-block" v-if="showAddModal">
      <div class="modal-dialog">
        <div class="modal-content">
          <form id="form" action="#" method="post">
            <div class="modal-body">
              <input id="form-uuid" type="hidden">

              <label for="form-title" class="form-label">Title</label>
              <input type="text" id="form-title" name="title" class="form-control" v-model="todoItems.title">
              <div id="form-title-invalid-feedback" class="invalid-feedback"></div>

              <textarea class="form-control mt-2" placeholder="Description..." v-model="todoItems.description"></textarea>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" @click="showAddModal = false">Close</button>
              <button type="button" class="btn btn-success" @click="save()">Save</button>
            </div>
          </form>
        </div>
      </div>
    </div>

    <div id="remove-confirmation-modal" class="modal d-block" v-if="showRemoveConfirmationModal">
      <div class="modal-dialog">
        <div class="modal-content">
          <form id="form" action="#" method="post">
            <div class="modal-body">
              Are you sure?
              This item "{{ deletedItem.title }}" will be deleted.
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" @click="showRemoveConfirmationModal = false">Cancel</button>
              <button type="button" class="btn btn-success" @click="remove()">Delete</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      todoList: [],
      showAddModal: false,
      showRemoveConfirmationModal: false,
      todoItems: {
        uuid: null,
        title: null,
        description: null,
        completed: false,
      },
      deletedItem: {},
    }
  },

  methods: {
    create() {
      this.todoItems = {
        uuid: null,
        title: null,
        description: null,
      }

      this.showAddModal = true
    },
    save() {
      this.todoItems.uuid = this.todoItems.uuid ? this.todoItems.uuid : this.generateUuid()

      let index = this.todoList.findIndex((item) => item.uuid === this.todoItems.uuid)

      if (index === -1) {
        this.todoList.push(this.todoItems)
      } else {
        this.todoList[index] = {...this.todoItems}
      }
      this.saveToLocalStorage()
      this.showAddModal = false
    },
    edit(uuid) {
      let editableItem = this.todoList.find((item) => item.uuid === uuid)
      this.todoItems = { ...editableItem }
      this.showAddModal = true
    },
    confirmRemove(uuid) {
      this.deletedItem = this.todoList.find((item) => item.uuid === uuid);
      this.showRemoveConfirmationModal = true;
    },
    remove() {
      let index = this.todoList.findIndex((item) => item.uuid === this.deletedItem.uuid)
      if (index !== -1) {
        this.todoList.splice(index, 1)
        this.saveToLocalStorage()
      }
      this.showRemoveConfirmationModal = false
    },
    toggleCompleted(todoItem) {
      todoItem.completed = !todoItem.completed
      console.log(todoItem.completed)
    },

    generateUuid() {
      return Math.random().toString(16).slice(2)
    },

    saveToLocalStorage() {
      localStorage.setItem("todoList", JSON.stringify(this.todoList))
    },

    loadFromLocalStorage() {
      const savedTodoList = localStorage.getItem("todoList")
      if (savedTodoList) {
        this.todoList = JSON.parse(savedTodoList)
      }
    },
  },
  mounted() {
    this.loadFromLocalStorage()
  },
}
</script>