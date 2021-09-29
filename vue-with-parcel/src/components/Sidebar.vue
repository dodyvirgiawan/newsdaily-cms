<template>
    <section id="sidebar-section">
        <div class="wrapper">
            <nav id="sidebar">
                <div class="card" style="margin: 5% 5%;">
                    <div class="card-body" style="background-color: #464aa7; border-radius: 5px 5px; color: #ffffff;">
                        <div class="user-container">
                            <div class="row">
                                <div class="col avatar" v-if="picture">
                                    <img :src="picture" alt="Avatar" class="avatar" style="display: block; margin: auto;">
                                </div>
                                <div class="col avatar" v-if="!picture">
                                    <img src="../assets/img/profilepicture.png" alt="Avatar" class="avatar" style="display: block; margin: auto;">
                                </div>

                                <br><br>
                                <div class="col" style="padding-right: 30px;">
                                    <div class="row" style="justify-content: center;">
                                        <small><strong>&nbsp;&nbsp;&nbsp;{{ email }}</strong></small>
                                    </div>
                                    <div class="row" style="justify-content: center;">
                                        <small><strong>&nbsp;&nbsp;&nbsp;{{ role }}</strong></small>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>


                <br>
                <div class="text-center">
                    <box-icon type='solid' name='dashboard' color='#5055be'></box-icon>
                </div>

                <div class="container">
                    <hr>
                    <h6><strong style="color: #464aa7">DASHBOARD</strong></h6>
                    <ul>
                        <li><button type="button" class="btn btn-block nav-btn" @click.prevent="changePage('homepage')" :class="{ 'alert-info': currentPage === 'homepage' }"><box-icon name='news' color='#5055be'></box-icon>&nbsp;&nbsp;&nbsp;News List</button></li>
                        <li><button type="button" class="btn btn-block nav-btn" @click.prevent="changePage('history')" :class="{ 'alert-info': currentPage === 'history' }"><box-icon name='history' color='#5055be'></box-icon>&nbsp;&nbsp;&nbsp;News History</li>
                        <li><button type="button" class="btn btn-block nav-btn" @click.prevent="changePage('addnews')" :class="{ 'alert-info': currentPage === 'addnews' }"><box-icon name='add-to-queue' type='solid' color='#5055be'></box-icon>&nbsp;&nbsp;&nbsp;Create News</li>
                    </ul>
                    
                    <hr>
                    <h6><strong style="color: #464aa7">SETTINGS</strong></h6>
                    <ul>
                        <li><button type="button" class="btn btn-block nav-btn"><box-icon name='user-circle' color='#5055be'></box-icon>&nbsp;&nbsp;&nbsp;Profile</li>
                        <li><button type="button" class="btn btn-block nav-btn"><box-icon name='brightness' color='#5055be'></box-icon>&nbsp;&nbsp;&nbsp;Settings</li>
                        <li><button type="button" class="btn btn-block nav-btn" @click.prevent="logout"><box-icon name='exit' color='#5055be'></box-icon>&nbsp;&nbsp;&nbsp;Logout</li>
                    </ul>
                </div>
            </nav>
        </div>
    </section>
</template>

<script>
import axios from 'axios'

export default {
    name: 'Sidebar',
    data: function() {
        return {
            id: '',
            picture: '',
            email: '',
            role: '',
        }
    },
    props: ['currentPage'],
    created: function() {
        axios({
            method: 'GET',
            url: `${this.baseServerURL}/users`,
            headers: {
                access_token: localStorage.getItem('access_token')
            }
        })
            .then(response => {
                this.id = response.data.id
                this.picture = response.data.picture
                this.email = response.data.email
                this.role = response.data.role

                this.$emit('storeUserInfo', {id: this.id, role: this.role, email: this.email})
            })
            .catch(err => {
                this.generateErrorResponse(err)
            })

    },
    methods: {
        changePage(page) {
            this.$emit('changePage', page)
        },
        logout() {
            this.$emit('logoutHandler')
        }
    }
}
</script>

<style>

</style>