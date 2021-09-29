<template>  
    <div>
        <Register 
            v-if="currentPage === 'register'" 
            @changePage='showPage'
        ></Register>

        <Login 
            v-if="currentPage === 'login'" 
            @changePage='showPage'
        ></Login>

        <Sidebar 
            v-if="isLoggedIn" 
            :currentPage="currentPage"
            @changePage='showPage'
            @logoutHandler='logout' 
            @storeUserInfo='storeUserInfo' 
        ></Sidebar>

        <Dashboard 
            v-if="isLoggedIn && currentPage === 'homepage'" 
            :loggedInUser="loggedInUser" 
            @editNews='editNews' 
            @changePage='showPage'
        ></Dashboard>

        <AddNewsForm 
            v-if="isLoggedIn && currentPage === 'addnews'" 
            @changePage='showPage'
        ></AddNewsForm>

        <EditNewsForm 
            v-if="isLoggedIn && currentPage === 'editnews'" 
            :editedNewsId='editedNewsId' 
            @changePage='showPage'
        ></EditNewsForm>

        <History 
            v-if="isLoggedIn && currentPage === 'history'" 
            @changePage='showPage'
        ></History>
    </div>
</template>

<script>
import Register from './views/Register.vue'
import Login from './views/Login.vue'
import Sidebar from './components/Sidebar.vue'
import Dashboard from './views/Dashboard.vue'
import AddNewsForm from './views/AddNewsForm.vue'
import EditNewsForm from './views/EditNewsForm.vue'
import History from './views/History.vue'
import boxicons from 'boxicons'
import Vue from 'vue'
import VueToast from 'vue-toast-notification'
import 'vue-toast-notification/dist/theme-sugar.css';

Vue.use(VueToast, {position: 'top-right'}) // Plugins declaration

Vue.mixin({ // Define getter and methods that will be used in more than one component.
    data: function() {
        return {
            get baseServerURL() {
                return 'http://localhost:3000'
            }
        }
    },
    methods: {
        generateErrorResponse(err) {
            let errorMessages = ''

            if(!err.response) { // Usually comes from axios
                errorMessages = err
            } else {
                if(Array.isArray(err.response.data.message)) {
                    err.response.data.message.forEach((el, idx) => {
                        errorMessages += `${el}`
                        errorMessages += idx === err.response.data.message.length - 1 ? '' : ',\n'
                    })
                } else {
                    errorMessages = err.response.data.message
                }
            }

            Vue.$toast.open({
                message: errorMessages,
                type: 'error',
            })
        },
        generateFetchingInfo(data) {
            Vue.$toast.open({
                message: `Fetching ${data} data... please wait`,
                type: 'info',
            })
        },
        generateFetchingSuccess(data) {
            Vue.$toast.open({
                message: `Fetching ${data} data success`,
                type: 'success',
            })
        }
    }
})

export default {
    data: function() {
        return {
            news: [],
            isLoggedIn: '',
            currentPage: 'login',
            loggedInUser: {
                id: '',
                role: '',
                email: ''
            },
            editedNewsId: {}
        }
    },
    components: {
        Register,
        Login,
        Sidebar,
        Dashboard,
        AddNewsForm,
        EditNewsForm,
        History,
        boxicons
    },
    created: function() {
        if(localStorage.getItem('access_token')) {
            this.isLoggedIn = true
            this.currentPage = 'homepage'
        }
    },
    methods: {
        showPage(page) {
            if(page !== 'register' && page !== 'login') this.isLoggedIn = true
            this.currentPage = page
        },
        logout() {
            if(gapi.auth2) gapi.auth2.getAuthInstance().signOut();
            localStorage.removeItem('access_token')
            this.isLoggedIn = false
            this.currentPage = 'login'

            swal('Success', "Successfully logged out", 'success')
        },
        storeUserInfo(payload) {
            const {id, email, role} = payload

            this.loggedInUser.id = id
            this.loggedInUser.email = email
            this.loggedInUser.role = role
        },
        editNews(payload) {
            this.editedNewsId = payload
            this.currentPage = 'editnews'
        }
    }
}
</script>

<style>

</style>