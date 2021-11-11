<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>
        <v-card align="left">
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
            <v-data-table :headers="headers" :items="todos" :search="search" item-key="note" show-expand>
                <template v-slot:[`item.actions`]="{ item }"> 
                    <v-icon 
                    small class="mr-2" color="green" @click="editItem(item)"> mdi-pencil </v-icon>
                    <v-icon small color="red" @click="deleteItem(item)"> mdi-delete </v-icon>
                </template>


                <template v-slot:[`item.priority`]="{ item }">
                    <v-chip v-show="item.priority == 'Penting'" color="red"  outlined>{{ item.priority }}</v-chip>
                    <v-chip v-show="item.priority == 'Biasa'" color="green"  outlined>{{ item.priority }}</v-chip>
                    <v-chip v-show="item.priority == 'Tidak Penting'" color="blue"  outlined>{{ item.priority }}</v-chip>
                </template>

                <template v-slot:expanded-item="{ headers, item }">
                    <td :colspan="headers.length">
                        Note: {{ item.note }}
                    </td> 
                </template>
            </v-data-table> 
        </v-card>
        <v-dialog v-model="dialog" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline"> From Todo </span>
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
                            :items="['Penting','Biasa','Tidak Penting']"
                            label="Priority"
                            required>
                        </v-select>

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
        </v-dialog>
    </v-main>
</template> 

<script>
export default {
    name: "List",
    data() {
        return {
            expanded:[],
            search: null,
            dialog: false,
            headers: [
                {
                    text: "Task",
                    align: "start",
                    sortable: true,
                    value: "task",
                },
                { text: "Priority", value: "priority" },
                { text: "Actions", value: "actions" },
                { text: "Note", value: "note", align: " d-none" },
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
                    note: "Wasting time",
                },
                {
                    task: "Masak",
                    priority: "Biasa",
                    note: "Indomi saat coding terbaik  gan",
                }
            ],
            formTodo: { task: null, priority: null, note: null },
            todo: [],
            editedIndex: -1,
            editedItem: {
                name: '',
                priority: '',
                note: '',
            },
            defaultItem: {
                name: '',
                priority: '',
                note: '',
            },
        };
    },

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'Tambah' : 'Edit'
      },
    },

    methods: {
        editItem (item) {
            this.editedIndex = this.todos.indexOf(item)
            this.formTodo = Object.assign({}, item)
            this.dialog = true
        },

        deleteItem (item) {
            const index = this.todos.indexOf(item)
            confirm('Yakin ingin menghapus item? [Y/N]') && this.todos.splice(index, 1)
        },

        save() {
            if (this.editedIndex > -1) {
            Object.assign(this.todos[this.editedIndex], this.formTodo)
            } else {
                this.todos.push(this.formTodo);
                this.resetForm();
                this.dialog = false;
            }
            this.cancel()
            
        },

        cancel() {
            this.dialog = false
            setTimeout(() => {
                this.resetForm()
                this.editedIndex = -1
            }, 300)
            // this.resetForm();
            // this.dialog = false;
        },

        resetForm() {
            this.formTodo = { task: null, priority: null, note: null };
        },
    },
};
</script> 