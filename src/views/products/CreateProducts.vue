<!-- <div id="app">
    <button type="button" @click="testToast">TEST BUTTON</button>
    <br><br>
    <transition name="fade" mode="out-in">
        <div v-if="testButClicked" class="alert" role="alert">
        Item successfully added to your cart
        </div>
    </transition>
    </div> -->
<template>
    <div class="py-4 container-fluid">
        <div class="col-lg-12 col-md-12 col-12">
            <div class="card mb-4">
                <div class="card-header pb-0">
                    <h4>Create New Products</h4>
                </div>
                <div class="card-body px-0 pt-0 pb-2">
                    
                    <argon-alert v-if="validation.error" color="info" icon="ni ni-like-2 ni-lg">
                        <strong>Success!</strong> Register Successfully
                    </argon-alert>
                        <form @submit.prevent="store">
                            <div class="form-group" style="margin-left: 2%;margin-right: 2%">
                                <label for="name" class="font-weight-bold">Name</label>
                                <input type="text" class="form-control" v-model="post.name" placeholder="Masukkan Name">
                                <!-- validation -->
                                <argon-alert v-if="validation.name" color="danger" icon="ni ni-fat-remove ni-lg">
                                    {{ validation.name[0] }}
                                </argon-alert>

                            </div>
                            <div class="form-group" style="margin-left: 2%;margin-right: 2%">
                                <label for="sku" class="font-weight-bold">SKU</label>
                                <input type="text" class="form-control" v-model="post.sku" placeholder="Masukkan SKU">
                                <!-- validation -->
                                <argon-alert v-if="validation.sku" color="danger" icon="ni ni-fat-remove ni-lg">
                                    {{ validation.sku[0] }}
                                </argon-alert>
                            </div>
                            <div class="form-group" style="margin-left: 2%;margin-right: 2%">
                                <label for="price" class="font-weight-bold">Price</label>
                                <input type="number" class="form-control" v-model="post.price" placeholder="Masukkan Price">
                                <!-- validation -->
                                <argon-alert v-if="validation.price" color="danger" icon="ni ni-fat-remove ni-lg">
                                    {{ validation.price[0] }}
                                </argon-alert>
                            </div>
                            <div class="form-group" style="margin-left: 2%;margin-right: 2%">
                                <label for="quantity" class="font-weight-bold">Quantity</label>
                                <input type="number" class="form-control" v-model="post.quantity" placeholder="Masukkan Quantity">
                                <!-- validation -->
                                <argon-alert v-if="validation.quantity" color="danger" icon="ni ni-fat-remove ni-lg">
                                    {{ validation.quantity[0] }}
                                </argon-alert>
                            </div>
                            <router-link :to="{name: 'Products'}" class="btn btn-md btn-success" style="margin-left: 2%;margin-right: 1%">Back</router-link>
                            <!-- <button type="button" @click="testToast">TEST BUTTON</button> -->
                            <button type="submit" class="btn btn-primary" @click="testToast">Simpan</button>
                        </form>                        
                    </div>
                </div>
            </div>
        </div>
</template>

<script>
import { reactive, ref } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'
import ArgonAlert from "@/components/ArgonAlert.vue";
import Swal from 'sweetalert2';

export default {

    components: {
        ArgonAlert,
    },

    data() {
        return {
            // validation: false,
            testButClicked: false
            }
        },
    methods: {
        testToast() {
        this.validation = true;
        // this.testButClicked = true;
        }
    },
    watch:{
        // testButClicked(val){
        // if (val){
        //     setTimeout(()=>this.testButClicked=false,1000);
        // }
        // },
        validation(val){
        if (val){
            setTimeout(()=>this.validation=false,6000);
        }
        }
    },

    setup() {

        //state token
        const token = localStorage.getItem('token')

        //state posts
        const post = reactive({
            name: '',
            sku: '',
            price: '',
            quantity: ''
        })

        //state validation
        const validation = ref([])
        console.log(validation)
        //vue router
        const router = useRouter()

        //method store
        function store() {

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

            let name = post.name
            let sku = post.sku
            let price = post.price
            let quantity = post.quantity

            axios.post('http://localhost:8000/api/products/create', {
                name: name,
                sku: sku,
                price: price,
                quantity: quantity
            }).then(() => {
                
                Swal.fire({
                    title: 'Success!',
                    text: 'Berhasil menambahkan produk',
                    icon: 'success',
                    confirmButtonText: 'Oke',
                    timer: 3000
                })
                //redirect ke post index
                router.push({
                    name: 'Products'
                })

            }).catch(error => {
                //assign state validation with error 
                validation.value = error.response.data.error
            })

        }

        //return
        return {
            post,
            validation,
            router,
            ArgonAlert,
            store
        }

    }

}
</script>
