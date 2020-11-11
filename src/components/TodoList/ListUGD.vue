<template>
    <v-main class="list">
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
                <v-btn color="success" dark @click="dialog = true">
                    Tambah
                </v-btn>
                 
            </v-card-title>
            <v-data-table :headers="headers" 
                            :items="todos" 
                            :search="search" 
                            :single-expand="singleExpand" 
                            :expanded.sync="expanded" 
                            :show-select="showSelect" 
                            item-key="task" 
                            show-expand 
                            class="elevation-1">
                <template v-slot:[`item.priority`]="{ item }">
                    <td>
                        <v-chip v-if="item.priority == 'Penting'" color="red" outlined>
                            {{ item.priority }}
                        </v-chip>
                        <v-chip v-else-if="item.priority == 'Biasa'" color="success" outlined>
                            {{ item.priority }}
                        </v-chip>
                        <v-chip v-else-if="item.priority == 'Tidak penting'" color="primary" outlined>
                            {{ item.priority }}
                        </v-chip>
                    </td>
                </template>
                <template v-slot:expanded-item="{ headers, item }">
                    <td :colspan="headers.length">
                        More info about {{ item.task }}
                    </td>
                </template>
                <template v-slot:[`item.actions`]="{ item }">
                    <v-btn small class="mr-2" @click="editItem(item)">
                        edit
                    </v-btn>
                    <v-btn small @click="deleteItem(item)">
                        delete
                    </v-btn>
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
                            :items="['Penting', 'Biasa', 'Tidak penting']"
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
                    <v-btn color="blue darken-1" text @click="cancel">
                        Cancel
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="save">
                        Save
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" persistent max-width="500px">
          <v-card>
            <v-card-title class="headline">Are you sure you want to delete this task?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="cancelDelete">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
              <v-spacer></v-spacer>
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
                expanded: [],
                singleExpand: false,
                search: null,
                dialog: false,
                dialogDelete: false,
                indexwoi:-1,
                slots:[
                    'Penting',
                    'Tidak Penting',
                ],
                headers: [
                    {
                        text: "Task",
                        align: "start",
                        sortable: true,
                        value: "task",
                    },
                    { text: "Priority", value: "priority" },
                    { text: "Note", value: "note" },
                    { text: "Actions", value: "actions" },
                    { text: "", value: "data-table-expand" },
                ],
                todos: [
                    {
                        task: "bernafas",
                        priority: "Penting",
                        note: "huffttt",
                    },
                    {
                        task: "nongkrong",
                        priority: "Tidak penting",
                        note: "bersama tman2",
                    },
                    {
                        task: "masak",
                        priority: "Biasa",
                        note: "masak air 500ml",
                    },
                ],
                formTodo: {
                    task: null,
                    priority: null,
                    note: null,
                },
            };
        },

        computed:{
            showSelect(){
                 return this.isEnabled('header.data-table-select') || this.isEnabled('item.data-table-select')
            }
        },

        watch:{
            enabled (slot) {
                if (slot === 'no-data') {
                    this.items = []
                } else if (slot === 'no-results') {
                    this.search = '...'
                } else {
                    this.search = null
                    this.items = todos
                }
            },
        },
        methods: {
            isEnabled (slot) {
                return this.enabled === slot
            },

            save() {
                if(this.indexwoi == -1){
                    this.todos.push(this.formTodo);    
                }else{
                    Object.assign(this.todos[this.indexwoi], this.formTodo);
                }
                this.resetForm();
                this.dialog = false;
            },
            cancel() {
                this.resetForm();
                this.dialog = false;
            },
            resetForm() {
                this.formTodo = {
                    task: null,
                    priority: null,
                    note: null,
                };
                this.indexwoi = -1;
            },
            editItem(item){
                this.indexwoi = this.todos.indexOf(item);
                this.editedItem = Object.assign({}, item);
                this.formTodo = {
                    task: item.task,
                    priority: item.priority,
                    note: item.note,
                };
                this.dialog = true;
            },
            deleteItem(item){
                this.indexwoi = this.todos.indexOf(item);
                this.dialogDelete = true;
                console.log(indexwoi);
            },
            cancelDelete(){
                this.dialogDelete = false;
                this.indexwoi = -1;
            },
            deleteItemConfirm(){
                this.todos.splice(this.indexwoi,1);
                this.cancelDelete();
            },

        },
    };
</script>