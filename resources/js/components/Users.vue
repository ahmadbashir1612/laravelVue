<template>
<div class="container">
    <div class="row mt-5">
          <div class="col-md-12">
            <div class="card">
              <div class="card-header">
                <h3 class="card-title">Users Table</h3>

                <div class="card-tools">
                    <button class="btn btn-success" data-toggle="modal" 
                    data-target="#addNew"> Add new 
                        <i class="fas fa-user-plus fa-fw"></i>
                    </button>
                </div>
              </div>
              <!-- /.card-header -->
              <div class="card-body table-responsive p-0">
                <table class="table table-hover">
                  <thead>
                    <tr>
                      <th>ID</th>
                      <th>Name</th>
                      <th>Email</th>
                      <th>Type</th>
                      <th>Registered at</th>
                      <th>Modify</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="user in users" :key="user.id">
                      <td>{{user.id}}</td>
                      <td>{{user.name}}</td>
                      <td>{{user.email}}</td>
                       <td>{{user.type|upText}}</td>
                       <td>{{user.created_at|myDate}}</td>
                  
                      <td>
                          <a href="#" >
                            <i class="fa fa-edit blue"/>
                          </a>
                        /  
                          <a href="#" @click="deleteUser(user.id)">
                             <i class="fa fa-trash blue"/> 
                          </a>
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>
              <!-- /.card-body -->
            </div>
            <!-- /.card -->
          </div>
        </div>

        <div class="modal fade" id="addNew" tabindex="-1" role="dialog" aria-labelledby="exampleModalCenterTitle" aria-hidden="true">
  <div class="modal-dialog modal-dialog-centered" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="exampleModalLongTitle">Add new</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>

    <form @submit.prevent="createUser">
      <div class="modal-body">

        <div class="form-group">
          <input v-model="form.name" type="text" name="name"
            placeholder="Name"
            class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
          <has-error :form="form" field="name"></has-error>
        </div>

        <div class="form-group">
          <input v-model="form.email" type="text" name="email"
            placeholder="Email"
            class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
          <has-error :form="form" field="email"></has-error>
        </div>

        <div class="form-group">
          <input v-model="form.password" type="text" name="password"
            placeholder="Password"
            class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
          <has-error :form="form" field="password"></has-error>
        </div>

        <div class="form-group">
          <select v-model="form.type" type="text" name="type" class ="form-control" :class="{
           'is-invalid' : form.errors.has('type')}">
            <option value="">Select User Role</option>
             <option value="admin">Admin</option>
              <option value="user">Standard User</option>
               <option value="author">Author</option>
          </select>
          <has-error :form="form" field="type"></has-error>
        </div>
        
      </div>



      <div class="modal-footer">
        <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
        <button type="submit" class="btn btn-primary">Create</button>
      </div>
    </form>
    </div>
  </div>
</div>
        </div>
</template>

<script>
    export default {

      data(){
        return{
          users:{},
          form:new Form({
            name:'',
            email:'',
            password:'',
            type:'',
            photo:''

            
          })

          
        }
      },

      methods:{

        deleteUser(id){

          swal.fire({
            title: 'Are you sure?',
            text: "You won't be able to revert this!",
            type: 'warning',
            showCancelButton: true,
            confirmButtonColor: '#3085d6',
            cancelButtonColor: '#d33',
            confirmButtonText: 'Yes, delete it!'
          }).then((result) => {
            if (result.value) {

              this.form.delete('api/user/'+id).then(()=>{
   
              swal.fire(
                'Deleted!',
                'Your file has been deleted.',
                'success'
              )
              Fire.$emit('AfterCreate');
              })
              .catch(()=>{
              swal.fire(
                'Failed!',
                'There was something wrong.',
                'warning'
              )
              })
            }
          })
        },

        loadUsers(){
          axios.get("api/user").then(({data})=>(this.users=data.data));
          },

        createUser(){
            this.$Progress.start();

            this.form.post('api/user')
            .then(()=>{

            Fire.$emit('AfterCreate');
            $('#addNew').modal('hide');       
            toast.fire({
              type: 'success',
              title: 'User created successfully'
            })

            this.$Progress.finish();
            })
            .catch(()=>{

            });     
            
        }
      },
        mounted() {

            this.loadUsers();
            // setInterval(() => {
            //   this.loadUsers()
            // }, 3000);
            Fire.$on('AfterCreate',()=>{
              this.loadUsers();
            })
            console.log('Component mounted.')
        }
    }
</script>
