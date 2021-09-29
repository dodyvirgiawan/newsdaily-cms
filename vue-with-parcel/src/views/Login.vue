<template>
    <section id="login-page">
        <div class="container" style="width: 80vw; margin-top: 10%;">
            <div class="text-center">
                <h1 class="title">News Daily</h1>
            </div>

            <br>
            <div class="card">
                <div class="card-body">
                    <form id="login-form">
                        <div class="form-group">
                            <input class="form-control" type="email" id="login-email" placeholder="Email address" v-model="loginData.email">
                        </div>
                        <div class="form-group">
                            <input class="form-control" type="password" id="login-password" placeholder="Password" v-model="loginData.password">
                        </div>
                        <button type="button" class="btn btn-primary btn-block" @click="loginButtonHandler">Login</button>
                    </form>

                    <br>
                    <div class="text-center">
                        <small>Don't have an account yet?</small>&nbsp;&nbsp;<button class="btn btn-info btn-sm" @click="changePage('register')">Sign Up</button><br>
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
    name: 'Login',
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
            loginData: {
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
        loginButtonHandler() {
            axios({
                method: 'POST',
                url: `${this.baseServerURL}/login`,
                data: {email: this.loginData.email, password: this.loginData.password}
            })
                .then(response => {
                    localStorage.setItem('access_token', response.data.access_token)
                    this.loginData = {}
                    
                    swal('Success', "Successfully logged in", 'success')

                    this.changePage('homepage')
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

                    swal('Success', "Successfully logged in", 'success')

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