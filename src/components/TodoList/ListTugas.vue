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
                <v-select style="margin-right:30px; width:25px"
                    v-model="sortingByP"
                    :items="['Penting','Tidak penting']"
                    @input="sortBy('idPriority',sortingByP)">
                </v-select>

                <v-btn color="success" dark @click="dialog = true">
                    Tambah
                </v-btn>           
            </v-card-title>
            <v-data-table   v-model="selected"
                            :headers="headers" 
                            :items="todos" 
                            :search="search"
                            expanded.sync="expanded"
                            item-key="note"
                            show-expand
                            show-select
                            class = "elevation-1" >
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
                        <br>
                        <h2 style="text-align:left">Note : </h2>
                        <br>
                        <h5 style="text-align:left">{{ item.note }}</h5>
                        <br>
                    </td>
                </template>
                
                <template v-slot:[`item.actions`]="{ item }">
                    <!--<v-btn small class="mr-2" @click="editItem(item)">
                        edit
                    </v-btn>
                    <v-btn small @click="deleteItem(item)">
                        delete
                    </v-btn>-->
                   
                    <v-icon color="blue" @click="editItem(item)">edit</v-icon>
                    <v-icon color="red" @click="deleteItem(item)">delete</v-icon>
                </template>
            </v-data-table>
        </v-card>
        <br>
    
        <v-card v-if="selected.length">
            <p style="text-align:left">Delete Multiple Task:</p>
            <ul>
                <li align="start" v-for="(todo,index) in selected" :key="index">{{ todo.task }}</li>
            </ul>
            <div>
                <v-btn color="red" dark @click="deleteTaskSelected">Hapus Semua</v-btn>
            </div>
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
                expanded: [], //buat expand
                singleExpand: false, //buat expand
                search: null, //buat search
                dialog: false,  //buat dialog add dan delete
                dialogDelete: false, // buat dialog are u sure delete
                indexwoi:-1,
                sortingByP: null,
                selected:[],
                i:0,
                indexSelectDelete:null,
                headers: [
                    {
                        text: "Task",
                        align: "start",
                        sortable: true,
                        value: "task",
                    },
                    { text: "Priority", value: "priority" },
                    { text: "Actions", value: "actions" },
                    { text: "", value:"data-table-select"}
  
                ],
                todos: [
                    {
                        task: "bernafas",
                        priority: "Penting",
                        note: "huffttt",
                        idPriority:3,
                    },
                    {
                        task: "nongkrong",
                        priority: "Tidak penting",
                        note: "bersama tman2",
                        idPriority:1,
                    },
                    { 
                        task: "masak",
                        priority: "Biasa",
                        note: "masak air 500ml",
                        idPriority:2,
                    },
                ],
                formTodo: {
                    task: null,
                    priority: null,
                    note: null,
                    idPriority:null,
                },
            };
        },
        methods: {
            save() {
                if(this.formTodo.priority == "Penting")
                {
                    this.formTodo.idPriority = 3;
                }else if(this.formTodo.priority == "Biasa")
                {
                    this.formTodo.idPriority = 2;
                }else if(this.formTodo.priority == "Tidak penting")
                {
                    this.formTodo.idPriority = 1;
                }

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
                    idPriority:null,
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
            },
            cancelDelete(){
                this.dialogDelete = false;
                this.indexwoi = -1;
            },
            deleteItemConfirm(){
                this.todos.splice(this.indexwoi,1);
                this.cancelDelete();
            },
            sortBy(prop, x){
                if(x == "Penting"){
                    this.todos.sort((a,b) => a[prop] > b[prop] ? -1 : 1)
                }else{
                     this.todos.sort((a,b) => a[prop] < b[prop] ? -1 : 1)
                }
                
            },
            deleteTaskSelected() {
                for(this.i = 0; this.i <this.selected.length; this.i++){
                    this.indexSelectDelete = this.todos.indexOf(this.selected[this.i]);
                    this.todos.splice(this.indexSelectDelete, 1);
                }
                this.selected=[];
            }
        },
    };
</script>