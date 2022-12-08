<template>
  <div class="container top-0 position-sticky z-index-sticky">
    <div class="row">
      <div class="col-12">
        <navbar
          isBlur="blur  border-radius-lg my-3 py-2 start-0 end-0 mx-4 shadow"
          v-bind:darkMode="true"
          isBtn="bg-gradient-success"
        />
      </div>
    </div>
  </div>
  <main class="mt-0 main-content">
    <section>
      <div class="page-header min-vh-100">
        <div class="container">
          <div class="row justify-content-center">
            <div class="mx-auto col-xl-4 col-lg-5 col-md-7 d-flex flex-column mx-lg-0">
              <div class="card">
                <div class="pb-0 card-header text-start">
                  <h4 class="font-weight-bolder text-center">Sign In</h4>
                  <p class="mb-0 text-center">Enter your email and password</p>
                </div>
                <div class="card-body">
                  <argon-alert v-if="isSuccess" color="info" icon="ni ni-like-2 ni-lg">
                    <strong>Success!</strong> Register Successfully
                  </argon-alert>
                  <form @submit.prevent="login">
                    <div class="mb-3">
                      <input class="form-control" v-model="user.email" type="email" placeholder="Email" name="email" size="lg" />
                    </div>
                    <argon-alert v-if="validation.email" color="danger" icon="ni ni-fat-remove ni-lg">
                          {{ validation.email[0] }}
                    </argon-alert>
                    <div class="mb-3">
                      <input class="form-control" v-model="user.password" type="password" placeholder="Password" name="password" size="lg" />
                    </div>
                    <argon-alert v-if="validation.password" color="danger" icon="ni ni-fat-remove ni-lg">
                          {{ validation.password[0] }}
                    </argon-alert>
                    
                    <argon-switch id="rememberMe">Remember me</argon-switch>
                    <div class="mb-3" style="display: grid">
                      <button type="submit" class="btn btn-primary mb-0 text-center" @click="testToast" style="margin-top: 4%">LOGIN</button>
                    </div>
                    

                    <!-- <div class="text-center">
                      <argon-button
                        class="mt-4"
                        variant="gradient"
                        color="success"
                        fullWidth
                        size="lg"
                        @click.prevent="login"
                      >Sign in</argon-button>
                    </div> -->
                  </form>
                </div>
                <div class="px-1 pt-0 text-center card-footer px-lg-2">
                  <p class="mx-auto mb-4 text-sm">
                    Don't have an account?
                    <router-link 
                    :to="{ name: 'Signup' }"
                    class="text-success text-gradient font-weight-bold">
                    Sign up</router-link>
                  </p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
<!-- <template>
  <div class="container-fluid mt-5">
      <div class="row justify-content-center">
          <div class="col-md-4">
              <div v-if="loginFailed" class="alert alert-danger">
                  Email atau Password Anda salah.
              </div>
              <div class="card border-0 rounded shadow">
                  <div class="card-body">
                      <h4>LOGIN</h4>
                      <hr>
                      <form @submit.prevent="login">
                          <div class="form-group">
                              <label>Email address</label>
                              <input type="email" v-model="email" class="form-control"
                                  placeholder="Email Address">
                          </div>
                          <div v-if="validation.email" class="mt-2 alert alert-danger">
                              {{ validation.email[0] }}
                          </div>

                          <div class="form-group">
                              <label>Password</label>
                              <input type="password" v-model="password" class="form-control"
                                  placeholder="Password">
                          </div>
                          <div v-if="validation.password" class="mt-2 alert alert-danger">
                              {{ validation.password[0] }}
                          </div>
                          <button type="submit" class="btn btn-primary btn-block">LOGIN</button>
                      </form>
                  </div>
              </div>
          </div>
      </div>
  </div>

</template> -->

<script>
  import { reactive, ref } from 'vue';
  import { useRouter } from 'vue-router';
  import axios from 'axios';
  import Swal from 'sweetalert2';
  import Navbar from "@/examples/PageLayout/Navbar.vue";
  // import ArgonInput from "@/components/ArgonInput.vue";
  import ArgonAlert from "@/components/ArgonAlert.vue";
  import ArgonSwitch from "@/components/ArgonSwitch.vue";
  // import ArgonButton from "@/components/ArgonButton.vue";
  const body = document.getElementsByTagName("body")[0];

  export default {
    components: {
    Navbar,
    ArgonAlert,
    // ArgonInput,
    ArgonSwitch,
    // ArgonButton,
    },
    computed: {
    isSuccess() {
      return this.$store.state.successState
    }
    },
    created() {
      this.$store.state.hideConfigButton = true;
      this.$store.state.showNavbar = false;
      this.$store.state.showSidenav = false;
      this.$store.state.showFooter = false;
      body.classList.remove("bg-gray-100");
    },
    beforeUnmount() {
      this.$store.state.hideConfigButton = false;
      this.$store.state.showNavbar = true;
      this.$store.state.showSidenav = true;
      this.$store.state.showFooter = true;
      body.classList.add("bg-gray-100");
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

          //inisialisasi vue router on Composition API
          const router = useRouter()

          //state user
          const user = reactive({
              email: '',
              password: '',
          })

          //state validation
          const validation = ref([])

          //state loginFailed
          const loginFailed = ref(null)

          //method login
          function login() {

              //define variable 
              let email = user.email
              let password = user.password

              //send server with axios
              axios.post('http://localhost:8000/api/login', {
                      email,
                      password,
              })
              .then(response => {

                  if(response.data.success) {

                      //set token
                      localStorage.setItem('token', response.data.token)
                    
                      Swal.fire({
                        title: 'Success!',
                        text: 'Login isoh cuk',
                        icon: 'success',
                        confirmButtonText: 'Oke',
                        timer: 3000
                      })

                      //redirect ke halaman dashboard
                      return router.push({
                          name: '/'
                      })
                  }

                  //set state loggedIn to true
                  loginFailed.value = true


              }).catch(error => {
                if(error.response.data.success == false) {
                  Swal.fire({
                    title: 'Error!',
                    text: 'Datamu ra cocok blok goblok',
                    icon: 'error',
                    confirmButtonText: 'Oke',
                    timer: 3000
                  })
                  //redirect ke halaman dashboard
                  return router.push({
                      name: 'Signin'
                  })
                }
                console.log(error)
                  //set validation dari error response
                  validation.value = error.response.data
                  // console.log(validation.value)
              })

          }

          return {
              user,           // <-- state user
              validation,     // <-- state validation 
              loginFailed,    // <-- state loggedIn
              login,           // <-- method login

          }

      }

  }
</script>
<!-- 
<template>
  <div class="container top-0 position-sticky z-index-sticky">
    <div class="row">
      <div class="col-12">
        <navbar
          isBlur="blur  border-radius-lg my-3 py-2 start-0 end-0 mx-4 shadow"
          v-bind:darkMode="true"
          isBtn="bg-gradient-success"
        />
      </div>
    </div>
  </div>
  <main class="mt-0 main-content">
    <section>
      <div class="page-header min-vh-100">
        <div class="container">
          <div class="row justify-content-center">
            <div class="mx-auto col-xl-4 col-lg-5 col-md-7 d-flex flex-column mx-lg-0">
              <div class="card">
                <div class="pb-0 card-header text-start">
                  <h4 class="font-weight-bolder text-center">Sign In</h4>
                  <p class="mb-0 text-center">Enter your email and password</p>
                </div>
                <div class="card-body">
                  <argon-alert v-if="isSuccess" color="info" icon="ni ni-like-2 ni-lg">
                    <strong>Success!</strong> Register Successfully
                  </argon-alert>
                  <form role="form">
                    <div class="mb-3">
                      <input class="form-control" v-model="email" type="email" placeholder="Email" name="email" size="lg" />
                    </div>
                    <div class="mb-3">
                      <input class="form-control" v-model="password" type="password" placeholder="Password" name="password" size="lg" />
                    </div>
                    <argon-switch id="rememberMe">Remember me</argon-switch>

                    <div class="text-center">
                      <argon-button
                        class="mt-4"
                        variant="gradient"
                        color="success"
                        fullWidth
                        size="lg"
                        @click.prevent="store"
                      >Sign in</argon-button>
                    </div>
                  </form>
                </div>
                <div class="px-1 pt-0 text-center card-footer px-lg-2">
                  <p class="mx-auto mb-4 text-sm">
                    Don't have an account?
                    <router-link 
                    :to="{ name: 'Signup' }"
                    class="text-success text-gradient font-weight-bold">
                    Sign up</router-link>
                  </p>
                </div>
              </div>
            </div>
            
          </div>
        </div>
      </div>
    </section>
  </main>
</template> -->

<!-- <script>
import axios from 'axios';
import Navbar from "@/examples/PageLayout/Navbar.vue";
// import ArgonInput from "@/components/ArgonInput.vue";
import ArgonAlert from "@/components/ArgonAlert.vue";
import ArgonSwitch from "@/components/ArgonSwitch.vue";
import ArgonButton from "@/components/ArgonButton.vue";
const body = document.getElementsByTagName("body")[0];

export default {
  name: "signin",
  components: {
    Navbar,
    ArgonAlert,
    // ArgonInput,
    ArgonSwitch,
    ArgonButton,
  },

  data: () => ({
    email: '',
    password: '',
  }),

  methods: {
    store() {
          const data = new FormData()
          data.append('email', this.email)
          data.append('password', this.password)

            axios.post('http://localhost:8000/api/login', data).then((response) => {
                localStorage.setItem('tokenargon',response.data.token)
                //redirect ke post index
                this.$router.push({path: '/'})

            }).catch(() => {
              this.$router.push({path: '/signin'})
            })

        }
  },
  computed: {
    isSuccess() {
      return this.$store.state.successState
    }
  },
  created() {
    this.$store.state.hideConfigButton = true;
    this.$store.state.showNavbar = false;
    this.$store.state.showSidenav = false;
    this.$store.state.showFooter = false;
    body.classList.remove("bg-gray-100");
  },
  beforeUnmount() {
    this.$store.state.hideConfigButton = false;
    this.$store.state.showNavbar = true;
    this.$store.state.showSidenav = true;
    this.$store.state.showFooter = true;
    body.classList.add("bg-gray-100");
  },                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   
};
</script> -->

