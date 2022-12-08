<template>
    <div class="py-4 container-fluid">
        <div class="col-lg-12 col-md-12 col-12">
            <div class="card mb-4">
                <div class="card-header pb-0">
                    <h4>Show Products</h4>
                </div>
                <div class="card-body px-0 pt-0 pb-2">
                        <form @submit.prevent="update">
                            <div class="form-group" style="margin-left: 2%;margin-right: 2%">
                                <label for="title" class="font-weight-bold">Name</label>
                                <input type="text" class="form-control" v-model="post.name" placeholder="Masukkan Nama" readonly>
                                <!-- validation -->
                                <div v-if="validation.name" class="mt-2 alert alert-danger">
                                    {{ validation.name[0] }}
                                </div>
                            </div>
                            <div class="form-group" style="margin-left: 2%;margin-right: 2%">
                                <label for="content" class="font-weight-bold">Sku</label>
                                <input class="form-control" rows="4" v-model="post.sku" placeholder="Masukkan SKU" readonly>
                                <!-- validation -->
                                
                                <div v-if="validation.sku" class="mt-2 alert alert-danger">
                                    {{ validation.sku[0] }}
                                </div>
                            </div>
                            <div class="form-group" style="margin-left: 2%;margin-right: 2%">
                                <label for="title" class="font-weight-bold">Price</label>
                                <input type="number" class="form-control" v-model="post.price" placeholder="Masukkan Price" readonly>
                                <!-- validation -->
                                <div v-if="validation.price" class="mt-2 alert alert-danger">
                                    {{ validation.price[0] }}
                                </div>
                            </div>
                            <div class="form-group" style="margin-left: 2%;margin-right: 2%">
                                <label for="title" class="font-weight-bold">Quantity</label>
                                <input type="number" class="form-control" v-model="post.quantity" placeholder="Masukkan Quantity" readonly>
                                <!-- validation -->
                                <div v-if="validation.quantity" class="mt-2 alert alert-danger">
                                    {{ validation.quantity[0] }}
                                </div>
                            </div>
                            <router-link :to="{name: 'Products'}" class="btn btn-md btn-success" style="margin-left: 2%;margin-right: 1%">Back</router-link>
                        </form>                        
                    </div>
                </div>
            </div>
        </div>
</template>

<script>
import { reactive, ref, onMounted } from 'vue';
import { useRouter, useRoute } from 'vue-router';
import axios from 'axios';
import Swal from 'sweetalert2';

export default {

    setup() {

        //state token
        const token = localStorage.getItem('token')

        //state posts
        const post = reactive({
            name: '',
            sku: '',
            price: '',
            quantity: '',
        })

        //state validation
        const validation = ref([])

        //vue router
        const router = useRouter()

        //vue router
        const route = useRoute()

        //mounted
        onMounted(() => {
            //check Token exist
            if(!token) {
                Swal.fire({
                    title: 'Error!',
                    text: 'Login sek coeg',
                    icon: 'error',
                    confirmButtonText: 'Oke'
                })
                // alert('Login sek');
                return router.push({
                    name: 'Signin'
                })
            }
                
            //get data user
            axios.defaults.headers.common.Authorization = `Bearer ${token}`
            
            //get API from Laravel Backend
            axios.get(`http://localhost:8000/api/products/${route.params.id}`)
            .then(response => {

              //assign state posts with response data
              post.name    = response.data.name  
              post.sku    = response.data.sku  
              post.price    = response.data.price  
              post.quantity  = response.data.quantity  

            }).catch(error => {
                console.log(error.response)
            })

        })

        //return
        return {
            post,
            validation,
            router        }

    }

}
</script>
