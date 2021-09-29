<template>
  <section id="register-page">
        <div class="container" style="width: 80vw; margin-top: 10%;">
            <div class="text-center">
                <h1 class="title">News Daily</h1>
            </div>

            <br>
            <div class="card">
                <div class="card-body">
                    <form id="register-form">
                        <div class="form-group">
                            <input class="form-control" type="email" id="register-email" placeholder="Email address" v-model='registerData.email'>
                        </div>
                        <div class="form-group">
                            <input class="form-control" type="password" id="register-password" placeholder="Password" v-model='registerData.password'>
                        </div>
                        <button type="button" class="btn btn-primary btn-block" @click='registerButtonHandler'>Register</button>
                    </form>

                    <br>
                    <div class="text-center">
                        <small>Already have an account?</small>&nbsp;&nbsp;<button class="btn btn-info btn-sm" @click="changePage('login')">Sign In</button><br>
                        <small>or Sign In with Google Account</small><br><br>

                        <GoogleLogin 
                            :params="params" 
                            :renderParams="renderParams" 
                            :onSuccess="onSuccess" 
                            style="width: 100px; height: 30px; margin: auto"
                        ></GoogleLogin>
                        
                    </div>
                </div>
            </div>
            <br>
            <div class="text-center">
                <small style="color: rgb(180, 180, 180)">© Dody Virgiawan 2021</small>
            </div>
        </div>
    </section>
</template>

<script>
import GoogleLogin from 'vue-google-login'
import axios from 'axios'
import Vue from 'vue'
import VueToast from 'vue-toast-notification'
import 'vue-toast-notification/dist/theme-sugar.css';

Vue.use(VueToast, {position: 'top-right'})

export default {
    name: 'Register',
    data: function() {
        return {
            params: {
                client_id: "565704402001-d55hmku6tmke7ohg55ptieftr5763hdm.apps.googleusercontent.com"
            },
            renderParams: {
                width: 100,
                height: 30,
                longtitle: false
            },
            registerData: {
                email: '',
                password: ''
            }
        }
    },
    components: {
        GoogleLogin
    },
    methods: {
        changePage(page) {
            this.$emit('changePage', page)
        },
        registerButtonHandler() {
            axios({
                method: 'POST',
                url: `${this.baseServerURL}/register`,
                data: {email: this.registerData.email, password: this.registerData.password}
            })
                .then(response => {
                    Vue.$toast.open({
                        message: `Successfully registered with email ${response.data.email}. Please proceed to login`,
                        type: 'success',
                    })

                    this.registerData = {}
                    this.changePage('login')
                })
                .catch(err => {
                    this.generateErrorResponse(err)
                })
        },
        onSuccess(googleUser) {
            const id_token = googleUser.getAuthResponse().id_token;

            axios({
                method: 'POST',
                url: `${this.baseServerURL}/auth/google`,
                data: { id_token }
            }) 
                .then(response => {
                    localStorage.setItem('access_token', response.data.access_token)

                    Vue.$toast.open({
                        message: 'Successfully logged in',
                        type: 'success',
                    })

                    this.changePage('homepage')
                })
                .catch(err => {
                    this.generateErrorResponse(err)
                })
        }
    }
}
</script>

<style>

</style>