<template>
    <div class="py-4 container-fluid">
        <div class="col-lg-12 col-md-12 col-12">
            <div class="card mb-4">
                <div class="card-header pb-0">
                    <h4>Product Lists</h4>
                </div>
                <div class="card-body px-0 pt-0 pb-2">
                    <div class="table-responsive p-0">
                        <router-link :to="{ name: 'Create Products' }" class="btn btn-md btn-success"
                            style="margin-left: 2%;width: auto;">Create</router-link>
                        <div class="col-md-1" style="margin-left: 2%">
                            <select class="form-control" v-model="paging" v-on:change="getData()">
                                <option value="5">5</option>
                                <option value="25">25</option>
                                <option value="50">50</option>
                                <option value="100">100</option>
                            </select>
                        </div>
                        <table
                            class="table table-responsive table-hover align-items-center justify-content-center mb-04">
                            <thead>
                                <tr>
                                    <th class="text-secondary font-weight-bolder opacity-14 text-center" scope="col"
                                        style="width: auto;">No.</th>
                                    <th class="text-secondary font-weight-bolder opacity-14 text-center" scope="col"
                                        style="width: auto">User</th>
                                    <th class="text-secondary font-weight-bolder opacity-14 text-center" scope="col"
                                        style="width: auto">Name</th>
                                    <th class="text-secondary font-weight-bolder opacity-14 text-center" scope="col"
                                        style="width: auto">SKU</th>
                                    <th class="text-secondary font-weight-bolder opacity-14 text-center" scope="col"
                                        style="width: auto;">Price</th>
                                    <th class="text-secondary font-weight-bolder opacity-14 text-center" scope="col"
                                        style="width: auto">Quantity</th>
                                    <th class="text-secondary font-weight-bolder opacity-14 text-center" scope="col"
                                        style="width: auto;">Options</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="(data, index) in posts" :key="index">
                                    <td class="text-center" style="width: auto;">
                                        <h6 class="mb-0 text-center">{{ data.id }}</h6>
                                    </td>
                                    <td class="text-center" style="width: auto;">
                                        <h6 class="mb-0 text-center">{{ data.users.name }}</h6>
                                    </td>
                                    <td class="text-center" style="width: auto;">
                                        <h6 class="mb-0 text-center">{{ data.name }}</h6>
                                    </td>
                                    <td class="text-center" style="width: auto;">
                                        <h6 class="mb-0 text-center">{{ data.sku }}</h6>
                                    </td>
                                    <td class="text-center" style="width: auto;">
                                        <h6 class="mb-0 text-center">{{ data.price }}</h6>
                                    </td>
                                    <td class="text-center" style="width: auto;">
                                        <h6 class="mb-0 text-center">{{ data.quantity }}</h6>
                                    </td>
                                    <td class="text-center" style="width: auto;">
                                        <router-link :to="{ name: 'Edit Products', params: { id: data.id } }"
                                            class="btn btn-primary mr-1" style="margin-right: 2%"><i
                                                class="fa fa-pencil" aria-hidden="true"></i></router-link>
                                        <router-link :to="{ name: 'Show Products', params: { id: data.id } }"
                                            class="btn btn-info mr-1" style="margin-right: 2%"><i class="fa fa-eye"
                                                aria-hidden="true"></i></router-link>
                                        <!-- <button class="btn btn-info mr-1" style="margin-right: 2%"><i class="fa fa-eye"
                                                aria-hidden="true"></i></button> -->
                                        <button v-on:click="postDelete(data.id)" class="btn btn-danger mr-1"><i
                                                class="fa fa-times" aria-hidden="true"></i></button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                        <button v-if="prev_page_url" v-on:click.prevent="gantiHalaman(prev_page_url)"
                            class="btn btn-primary">Prev</button>
                        <button v-if="next_page_url" v-on:click.prevent="gantiHalaman(next_page_url)"
                            class="btn btn-primary">Next</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import { onMounted, ref } from 'vue';
import { useRouter } from 'vue-router';
import Card from "@/examples/Cards/Card.vue";
import Swal from 'sweetalert2';


export default {

    data() {
        return {
            // validation: false,
            url: '',
            data_produk: [],
            next_page_url: '',
            prev_page_url: '',
            paging: ''
        }
    },

    methods: {
        getData() {
            const url = 'http://localhost:8000/api/get-all-product';
            const paging = 10;
            axios.get(url, {
                params: {
                    paging: paging
                }
            })
                .then(resp => {
                    console.log(resp.data);
                    this.data_produk = resp.data.data;
                    this.next_page_url = resp.data.next_page_url;
                    this.prev_page_url = resp.data.prev_page_url;
                })
                .catch(err => {
                    alert('error');
                    console.log(err);
                })
        },
        gantiHalaman(url) {
            this.url = url;
            this.getData();
        },

        postDelete(id) {

            Swal.fire({
                title: 'Are you sure?',
                text: "You won't be able to revert this!",
                type: 'warning',
                showCancelButton: true,
                confirmButtonColor: '#3085d6',
                cancelButtonColor: '#d33',
                confirmButtonText: 'Yes, delete it!'
            }).then((result) => {
                if (result.value) {
                    Swal.fire(
                        'Deleted!',
                        'Your file has been deleted.',
                        'success'
                    ),

                    axios.delete(`http://localhost:8000/api/products/delete/${id}`)
                        .then(() => {
                            // splice posts
                            const index = this.posts.findIndex(post => post.id === id) // find the post index
                            if (~index) {
                                // if the post exists in array
                                this.posts.splice(index, 1)
                            }
                        })
                        .catch(error => {
                            console.log(error);
                    })
                }
            });
        }
    },

    setup() {

        //state token
        const token = localStorage.getItem('token')

        //inisialisasi vue router on Composition API
        const router = useRouter()

        //reactive state
        let posts = ref([])

        //mounted
        onMounted(() => {

            //check Token exist
            if (!token) {
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

            const url = 'http://localhost:8000/api/get-all-product';
            const paging = 10;

            //get data user
            axios.defaults.headers.common.Authorization = `Bearer ${token}`

            //get API from Laravel Backend
            axios.get(url, {
                params: {
                    paging: paging
                }
                
            }) 
                .then(response => {
                    console.log(response)
                    //assign state posts with response data
                    posts.value = response.data.data

                    posts.value.next_page_url = response.data.next_page_url
                    posts.value.prev_page_url = response.data.prev_page_url
                    console.log(response.data.next_page_url)

                }).catch(error => {
                    posts.value = error.response
                    console.log(error)
                })
        })

        // function getData() {
        //     this.url = "{{ url('get-master-product-paging') }}"
        //     this.getData();
        //         axios.get(this.url)
        //             .then(resp => {
        //                 console.log(resp.data);
        //             })
        //             .catch(err => {
        //                 alert('error');
        //                 console.log(err);
        //             })
        //     }

        //return
        return {
            posts,
            Card,
            Swal
            // getData
            // postDelete
        }

    }
}
</script>
