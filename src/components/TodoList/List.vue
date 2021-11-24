<template>
  <v-main class="list">
      <v-snackbar
            v-model="Penting"
            :timeout="1000"
            :value="true"
            color="red"
            right
            top
          >
            Penting
          </v-snackbar>
          <v-snackbar
            v-model="Tidak_Penting"
            :timeout="1000"
            :value="true"
            color="blue"
            bottom
          >
        Tidak Penting
          </v-snackbar>
          <v-snackbar
            v-model="Biasa"
            :timeout="1000"
            :value="true"
            color="green"
            left
            top
          >
            Biasa
          </v-snackbar>

    <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>
    <v-card>
      <v-card-title>
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Search"
          single-line
          hide-details
        ></v-text-field>
        <v-spacer></v-spacer>
        <v-btn color="success" dark @click="dialog = true"> Tambah </v-btn>
      </v-card-title>
      <v-data-table
        :headers="headers"
        :items="todos"
        :search="search"
        :expanded.sync="expanded"
        item-key="note"
        show-expand
      >
        <template v-slot:[`item.actions`]="{ item }">
          <v-icon color="green" small class="mr-2" @click="editItem(item)"
            >mdi-pencil</v-icon
          >
          <v-icon color="red" small @click="deleteItem(item)">
            mdi-delete
          </v-icon>
        </template>

        <template v-slot:[`item.priority`]="{item}">
          <v-icon color="yellow" medium class="mr-2" @click='cek(item)'>mdi-alert-circle-check</v-icon>
        </template>

        <template v-slot:expanded-item="{ headers, item }">
          <td :colspan="headers.length">
            <p v-bind:class="{ 'pentingg': item.priority=='Penting', 'tidak_pentingg': item.priority=='Tidak Penting',  'biasaa': item.priority=='Biasa'}">Note: {{ item.note }}</p>
          </td>
        </template>
      </v-data-table>
    </v-card>
    <v-dialog v-model="dialog" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">Form Todo</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-text-field
              v-model="formTodo.task"
              label="Task"
              required
            ></v-text-field>
            <v-select
              v-model="formTodo.priority"
              :items="['Penting', 'Biasa', 'Tidak Penting']"
              label="Priority"
              required
            ></v-select>

            <v-textarea
              v-model="formTodo.note"
              label="Note"
              required
            ></v-textarea>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="cancel"> Cancel </v-btn>
          <v-btn color="blue darken-1" text @click="save"> Save </v-btn>
        </v-card-actions>
      </v-card>

      <v-dialog v-model="dialogDelete" max-width="500px">
        <v-card>
          <v-card-title class="headline"
            >Are you sure you want to delete this item?</v-card-title
          >
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="green" text @click="closeDelete">Cancel</v-btn>
            <v-btn color="red" text @click="deleteItemConfirm">OK</v-btn>
            <v-spacer></v-spacer>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <v-dialog v-model="dialog" persistent max-width="600px">
        <v-card>
          <v-card-title>
            <span class="headline">Form Todo</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-text-field v-model="formTodo.task" label="Task" required>
              </v-text-field>
              <v-select
                v-model="formTodo.priority"
                :items="['Penting', 'Biasa', 'Tidak penting']"
                label="Priority"
                required
              >
              </v-select>
              <v-textarea v-model="formTodo.note" label="Note" required>
              </v-textarea>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="cancel"> Cancel </v-btn>
            <v-btn color="blue darken-1" text @click="save"> Save </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-dialog>
  </v-main>
</template>

<script>
export default {
  name: "List",
  data() {
    return {
      search: null,
      dialog: false,
      dialogDelete: false,
      headers: [
        {
          text: "Task",
          align: "start",
          sortable: true,
          value: "task",
        },
        { text: "Priority", value: "priority" },
        {},
        { text: "Actions", value: "actions" },
      ],
      todos: [
        {
          task: "Coding",
          priority: "Penting",
          note: "Code for your life",
        },
        {
          task: "Gaming",
          priority: "Tidak Penting",
          note: "Wasting Time",
        },
        {
          task: "Masak",
          priority: "Biasa",
          note: "Indomi saat coding terbaik gan",
        },
      ],
      edit: -1,
      delete: -1,
      Penting: false,
      Tidak_Penting: false,
      Biasa: false,
      formTodo: { task: null, priority: null, note: null },
    };
  },

  methods: {
    cek(item){
        if(item.priority=='Penting'){
            this.Penting = true;
        }
        else if(item.priority=='Tidak Penting'){
            this.Tidak_Penting = true;
        }
        else if(item.priority=='Biasa'){
            this.Biasa = true;
        }
    },
    save() {
      if (this.edit > -1) {
        (this.editTodo.task = this.formTodo.task),
          (this.editTodo.priority = this.formTodo.priority),
          (this.editTodo.note = this.formTodo.note),
          (this.dialog = false);
      } else {
        this.todos.push(this.formTodo);
        this.resetForm();
        this.dialog = false;
      }
    },
    cancel() {
      this.resetForm();
      this.dialog = false;
    },
    resetForm() {
      this.formTodo = { tasl: null, priority: null, note: null };
    },
    editItem(item) {
      (this.edit = this.todos.indexOf(item)),
        (this.formTodo.task = item.task),
        (this.formTodo.priority = item.priority),
        (this.formTodo.note = item.note),
        (this.dialog = true);
      this.editTodo = item;
    },
    deleteItem(item) {
      this.delete = this.todos.indexOf(item);
      (this.editTodo = item), (this.dialogDelete = true);
    },

    deleteItemConfirm() {
      this.todos.splice(this.delete, 1);
      this.closeDelete();
    },
    close() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editTodo = Object.assign({}, this.defaultItem);
        this.delete = -1;
      });
    },
    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editTodo = Object.assign({}, this.defaultItem);
        this.delete = -1;
      });
    },
  },
};
</script>

<style scoped>
.pentingg{
    background-color: red;
    color: white;
    border-radius: 6px 6px 6px 6px;
    text-align: left;
}

.tidak_pentingg{
    background-color: blue;
    color: white;
    border-radius: 6px 6px 6px 6px;
    text-align: left;
}

.biasaa{
    background-color: green;
    color: white;
    border-radius: 6px 6px 6px 6px;
    text-align: left;
}
</style>
