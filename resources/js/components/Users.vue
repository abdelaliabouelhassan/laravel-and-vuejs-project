<template>
    <div class="container">
        <section class="content mt-5">
            <div class="row">
                <div class="col-md-12">
                         <div class="card">
                            <div class="card-header">
                                <h3 class="card-title">All Users</h3>
                                <div class="card-tools">
                                        <button class="btn btn-success" data-toggle="modal" data-target="#AddNewModal" @click="newModal">ADD NEW <i class="fas fa-user-plus fa-fw"></i></button>
                                </div>
                            </div>
            <!-- /.card-header -->
            <div class="card-body">
                <table id="example1" class="table table-bordered table-striped">
                    <thead>
                    <tr>
                        <th>ID</th>
                        <th>User Name</th>
                        <th>Email</th>
                        <th>Type</th>
                        <th>Registerd At</th>
                        <th>Modify</th>
                    </tr>
                    </thead>
                    <tbody>
                    <tr v-for="user in users" :key="user.id">
                        <td>{{user.id}}</td>
                        <td>{{user.name}}</td>
                        <td>{{user.email}}</td>
                        <td>{{user.type}}</td>
                        <td>{{user.created_at | mydate}}</td>
                        <td><a href="javascript:void(0)" @click="EditModal(user)">Edit <i class="fa fa-edit "></i></a>
                            /
                            <a href="javascript:void(0)" @click="DeleteUser(user.id)">Delete<i class="fa fa-trash red"></i></a>
                     </td>

                    </tr>


                    </tbody>

                </table>
            </div>
            <!-- /.card-body -->
        </div>
    </div>
            </div>
        </section>





<!--modal-->
        <div class="modal fade" id="AddNewModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLabel">{{createMode ? 'Add New User' :'Update This  User'}}</h5>

                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body">
                        <form @submit.prevent="createMode ? CreateUser() : EditUser()">
                            <div class="form-group">
                                <input v-model="form.name" type="text" name="name" placeholder="UserName"
                                       class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
                                <has-error :form="form" field="name"></has-error>
                            </div>

                            <div class="form-group">
                                <input v-model="form.email" type="email" name="email" placeholder="Email"
                                       class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
                                <has-error :form="form" field="email"></has-error>
                            </div>
                            <div class="form-group">

                                <select name="type"  v-model="form.type" class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">
                                    <option value="">Select Rule</option>
                                    <option value="1">Admin</option>
                                    <option value="2">mode</option>
                                    <option value="3">spectator</option>

                                </select>
                                <has-error :form="form" field="type"></has-error>

                            </div>
                            <div class="form-group">
                                <input v-model="form.password" type="password" name="password" placeholder="password"
                                       class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                                <has-error :form="form" field="password"></has-error>
                            </div>
                            <div class="modal-footer">
                                <button  type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                                <button type="submit" class="btn btn-primary">  {{createMode ? 'Create User' :'Update User'}} </button>
                            </div>

                        </form>
                    </div>

                </div>
            </div>
        </div>

    </div>

</template>

<script>
    export default {
        data () {

            return {
                createMode:true,
                users:{},
                // Create a new form instance
                form: new Form({
                    id:'',
                    name: '',
                    password: '',
                    type: '',
                    email:''
                })
            }
        },
        methods:{
            EditUser:function(){
                this.$Progress.start()

                this.form.put('api/user/' + this.form.id)
                    .then(()=>{
                        something.$emit('wherecreateuserloaddate');
                        $("#AddNewModal").modal('hide')

                        this.$Progress.finish()
                        Toast.fire({
                            icon: 'success',
                            title: 'Updated User Successfully'
                        })
                    })
                    .catch(()=>{
                        this.$Progress.decrease(20)
                        this.$Progress.fail()
                    })
            },
            CreateUser:function() {
                this.$Progress.start()
                this.form.post('api/user')
                    .then(()=>{
                        something.$emit('wherecreateuserloaddate');
                        $("#AddNewModal").modal('hide')

                        this.$Progress.finish()
                        Toast.fire({
                            icon: 'success',
                            title: 'User Created Successfully'
                        })
                    })
                    .catch(()=>{
                        this.$Progress.decrease(20)
                        this.$Progress.fail()
                    })

            },
            loadUsers:function () {
                axios.get('api/user').then(({data})=>(this.users = data.data))

            },
            DeleteUser:function (userid) {
                swalWithBootstrapButtons.fire({
                    title: 'Are you sure?',
                    text: "You won't be able to revert this!",
                    icon: 'warning',
                    showCancelButton: true,
                    confirmButtonText: 'Yes, delete it!',
                    cancelButtonText: 'No, cancel!',
                    reverseButtons: true
                }).then((result) => {

                    if (result.value) {

                        this.$Progress.start()
                        this.form.delete('api/user/'+userid).then(()=>{
                            swalWithBootstrapButtons.fire(
                                'Deleted!',
                                'Your file has been deleted.',
                                'success'
                            )
                            something.$emit('wherecreateuserloaddate');
                            this.$Progress.finish()
                        }).catch(()=>{
                            this.$Progress.start()
                            swalWithBootstrapButtons.fire(
                                'Cancelled',
                                'there some Probelms You Cant Delete This User ):',
                                'error'

                            )
                            this.$Progress.decrease(20)
                            this.$Progress.fail()
                        })


                    } else if (
                        /* Read more about handling dismissals below */
                        result.dismiss === Swal.DismissReason.cancel
                    ) {
                        this.$Progress.start()
                        swalWithBootstrapButtons.fire(
                            'Cancelled',
                            'Your imaginary file is safe :)',

                        )
                        this.$Progress.decrease(20)
                        this.$Progress.fail()
                    }
                })
            },
            newModal:function () {
                this.createMode = true
                this.form.reset();
                $("#AddNewModal").modal('show')
            }
            ,
            EditModal:function (user) {
                this.createMode = false
                $("#AddNewModal").modal('show')
                this.form.fill(user)

            }
        },
        created() {
          this.loadUsers()
            something.$on('wherecreateuserloaddate',()=>{
               this .loadUsers();
            });
        }
    }
</script>
