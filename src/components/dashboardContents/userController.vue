<template>   
    <v-container>     
        <v-card>       
            <v-container grid-list-md mb-0>
                 <h2 class="text-md-center">Data Sparepart</h2>         
            <v-layout row wrap style="margin:10px">           
                <v-flex xs6>              
                 <v-btn               
                 depressed               
                 dark               
                 rounded               
                 style="text-transform: none !important;"               
                 color = "green accent-3"               
                 @click="dialog = true">               
                 <v-icon size="18" class="mr-2">mdi-pencil-plus</v-icon>                   
                 Tambah Sparepart             
                 </v-btn>           
                </v-flex>           
                <v-flex xs6 class="text-right">               
                    <v-text-field                 
                    v-model="keyword"                 
                    append-icon="mdi-search"                 
                    label="Search"                 
                    hide-details></v-text-field>           
                </v-flex>         
            </v-layout>

            <v-data-table             
                :headers="headers"             
                :items="sparepart"             
                :search="keyword"             
                :loading="load">             
            <template v-slot:body="{ items }">               
                <tbody>                 
                    <tr v-for="(item,index) in items" :key="item.id">                   
                        <td>{{ index + 1 }}</td>                   
                        <td>{{ item.id }}</td>                   
                        <td>{{ item.name}}</td>                   
                        <td>{{ item.merk }}</td> 
                        <td>{{ item.jumlah }}</td>
                        <td>{{ item.created_at }}</td>                  
                        <td class="text-center">                     
                            <v-btn                        
                            icon                       
                            color="indigo"                       
                            light                       
                            @click="editHandler(item)">                       
                            <v-icon>mdi-pencil</v-icon>                     
                            </v-btn>     

                            <v-btn  
                            icon                       
                            color="error"                       
                            light                       
                            @click="deleteData(item.id)">                       
                            <v-icon>mdi-delete</v-icon>                     
                            </v-btn> 
                        </td>                 
                    </tr>              
                </tbody>            
            </template>          
            </v-data-table>       
            </v-container>     
        </v-card>
        <v-dialog v-model="dialog" persistent max-width="600px">       
            <v-card>         
                <v-card-title>           
                    <span class="headline">User Profile</span>         
                </v-card-title>         
                <v-card-text>           
                    <v-container>             
                        <v-row>               
                            <v-col cols="12">                 
                                <v-text-field label="ID*" v-model="form.id" required></v-text-field>               
                            </v-col>               
                            <v-col cols="12"> 
                                <v-text-field label="Name*" v-model="form.name" required></v-text-field>               
                            </v-col>
                            <v-col cols="12"> 
                                <v-select :item="select" label="Merk*" v-model="form.merk" required></v-select>              
                            </v-col> 
                            
                            <v-col cols="12"> 
                                <v-text-field label="Jumlah*" v-model="form.jumlah" required></v-text-field>               
                            </v-col>
                            <v-col cols="12"> 
                                <v-text-field label="Created At*" v-model="form.created_at" required></v-text-field>               
                            </v-col>              
                                        
                        </v-row>
                    </v-container>           
                    <small>*indicates required field</small> 
                </v-card-text>         
                <v-card-actions>           
                    <v-spacer></v-spacer>           
                    <v-btn color="blue darken-1" text @click="dialog = false">Close</v-btn>           
                    <v-btn color="blue darken-1" text @click="setForm()">Save</v-btn>         
                </v-card-actions>       
                </v-card>     
            </v-dialog>     
            <v-snackbar       
                v-model="snackbar"
                :color="color"       
                :multi-line="true"       
                :timeout="3000">       
                {{ text }}       
                <v-btn         
                dark         
                text         
                @click="snackbar = false">         
                Close       
                </v-btn>     
             </v-snackbar>   
             </v-container> 
        </template> 
    <script> 
    export default {    
        data () {       
            return {         
                dialog: false,         
                keyword: '',         
                headers: [             
                    {               
                        text: 'No',               
                        value: 'no',
                    },             
                    {               
                        text: 'ID',               
                        value: 'id'             
                    },             
                    {               
                        text: 'Name',               
                        value: 'name'             
                    },             
                    {               
                        text: 'Merk',               
                        value: 'merk'             
                    },
                    {               
                        text: 'Jumlah',               
                        value: 'jumlah'             
                    },             
                    {               
                        text: 'Created At',               
                        value: 'created_at'             
                    },         
                ],         
                sparepart: [],         
                snackbar: false,          
                color: null,         
                text: '',          
                load: false,
                form: {   
                    id: '',         
                    name : '',           
                    merk : '',           
                    jumlah : '',
                    created_at : ''         
                },         
                sparepart : new FormData,         
                typeInput: 'new',         
                errors : '',         
                updatedId : '',       
                }     
            },
             methods:{         
                getData()
                {             
                     var uri = this.$apiUrl + '/sparepart'             
                     this.$http.get(uri).then(response =>{                 
                         this.users=response.data.message             
                         })               
                    },         
                sendData()
                {             
                    this.user.append('ID', this.form.id);             
                    this.user.append('Name', this.form.name);
                    this.user.append('Merk', this.form.merk); 
                    this.user.append('Jumlah', this.form.jumlah);
                    this.user.append('Created At', this.form.created_at);            
                    var uri =this.$apiUrl + '/sparepart'             
                    this.load = true             
                    this.$http.post(uri,this.user).then(response =>{               
                        this.snackbar = true; //mengaktifkan snackbar               
                        this.color = 'green'; //memberi warna snackbar               
                        this.text = response.data.message; //memasukkan pesan ke snackba r               
                        this.load = false;               
                        this.dialog = false               
                        this.getData(); //mengambil data user               
                        this.resetForm();           
                        }).catch(error =>{               
                            this.errors = error               
                            this.snackbar = true;               
                            this.text = 'Try Again';               
                            this.color = 'red';               
                            this.load = false;           
                        })         
                    },         
                updateData()
                {             
                    this.user.append('ID', this.form.id);             
                    this.user.append('Name', this.form.name);
                    this.user.append('Merk', this.form.merk); 
                    this.user.append('Jumlah', this.form.jumlah); 
                    this.user.append('Created At', this.form.created_at);            
                    var uri = this.$apiUrl + '/sparepart/' + this.updatedId;             
                    this.load = true             
                    this.$http.post(uri,this.user).then(response =>{ 
                         this.snackbar = true; //mengaktifkan snackbar               
                         this.color = 'green'; //memberi warna snackbar               
                         this.text = response.data.message; //memasukkan pesan ke snackbar               
                         this.load = false;               
                         this.dialog = false               
                         this.getData(); //mengambil data user               
                         this.resetForm();               
                         this.typeInput = 'new';           
                        }).catch(error =>{               
                            this.errors = error               
                            this.snackbar = true;               
                            this.text = 'Try Again';               
                            this.color = 'red';               
                            this.load = false;               
                            this.typeInput = 'new';           
                        })         
                    },
                editHandler(item)
                {           
                     this.typeInput = 'edit';           
                     this.dialog = true;
                     this.form.id = item.id;
                     this.form.name = item.name;          
                     this.form.merk = item.merk;
                     this.form.jumlah = item.jumlah;
                     this.form.created_at = item.created_at;         
                     this.updatedId = item.id         
                 }, 
                deleteData(deleteId)
                { //mengahapus data             
                    var uri = this.$apiUrl + '/sparepart/' + deleteId; //data dihapus berdasarkan id 
                    this.$http.delete(uri).then(response =>{                
                        this.snackbar = true;                 
                        this.text = response.data.message;                 
                        this.color = 'green'                 
                        this.deleteDialog = false;                 
                        this.getData();             
                    }).catch(error =>{                 
                        this.errors = error                 
                        this.snackbar = true;                 
                        this.text = 'Try Again';                
                        this.color = 'red'; 
                        })         
                    },
                setForm()
                {             
                     if (this.typeInput === 'new') {               
                         this.sendData()             
                        } else {               
                            console.log("dddd")
                            this.updateData()             
                        }
                     },         
                resetForm()
                {             
                    this.form = {                
                        id : '',               
                        name : '',               
                        merk : '',
                        jumlah : '',
                        created_at : ''             
                    }         
                }
            },
            mounted(){         
                this.getData();     
        }
    } 
</script>