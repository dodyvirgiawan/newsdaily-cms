<template>
    <tr>
        <td>{{ index + 1 }}</td>
        <td>{{ eachNews.title }}</td>
        <td>{{ formattedContent }}</td>
        <td v-if="loggedInUser.role === 'admin'">
            <select class="form-control status-select" :class="{'alert-success': status === 'active', 'alert-danger': status === 'inactive', 'alert-warning': status === 'archived'}" v-model="status" @change="changeStatus">
                <option v-for="(eachStatus, index) in allStatus" :key="index" :value="eachStatus">{{ eachStatus }}</option>
            </select>
        </td>
        <td v-if="loggedInUser.role === 'staff'">
            <span class="alert" :class="{'alert-success': eachNews.status === 'active', 'alert-danger': eachNews.status === 'inactive', 'alert-warning': eachNews.status === 'archived'} "> {{ eachNews.status }} </span>
        </td> 
        <td>
            <img :src="imgUrl" style="max-width: 100%; height: auto;">
        </td>
        <td>{{ eachNews.User.email }}</td>
        <td>{{ eachNews.Category.name }}</td>
        <td v-if="loggedInUser.role === 'admin' || loggedInUser.id === eachNews.User.id">
            <button type="button" class="btn btn-success btn-sm btn-block" @click="editNews">Edit</button>
            <button type="button" class="btn btn-danger btn-sm btn-block" @click="deleteNews">Delete</button>
        </td>
        <td v-if="loggedInUser.role === 'staff'"></td>
    </tr>
</template>

<script>
import axios from 'axios'
import Vue from 'vue'
import VueToast from 'vue-toast-notification'
import 'vue-toast-notification/dist/theme-sugar.css';

Vue.use(VueToast, {position: 'top-right'}) // Plugins

export default {
    name: 'NewsRowData',
    data: function() {
        return {
            imgUrl: '',
            id: '',
            status: '',
            allStatus: ['active', 'inactive', 'archived']
        }
    },
    props: ['eachNews', 'loggedInUser', 'index'],
    created: function() {
        this.imgUrl = this.eachNews.imgUrl
        this.id = this.eachNews.id
        this.status = this.eachNews.status
    },
    computed: {
        formattedContent() {
            const charLimit = 600 // Character limit to display in UI. Prevent overly long text-content.

            if(this.eachNews.content.length > charLimit) {
                return `${this.eachNews.content.split('').splice(0, charLimit).join('')}...`
            } else {
                return this.eachNews.content
            }
        }
    },
    methods: {
        editNews() {
            this.$emit('editNews', this.id)
        },
        deleteNews() {
            swal({
                title: "Are you sure?",
                text: "Once deleted, you will not be able to undo this action",
                icon: "warning",
                buttons: true,
                dangerMode: true,
            })
                .then(willDelete => {
                    if(willDelete) {
                        axios({
                            method: 'DELETE',
                            url: `${this.baseServerURL}/news/${this.id}`,
                            headers: {
                                access_token: localStorage.getItem('access_token')
                            }
                        })
                            .then(response => {
                                this.$emit('fetchNews')
                                Vue.$toast.open({
                                    message: `News successfully deleted`,
                                    type: 'success',
                                })
                            })
                            .catch(err => {
                                this.generateErrorResponse(err)
                            })
                    }
                })
        },
        changeStatus() {
            axios({
                method: 'PATCH',
                url: `${this.baseServerURL}/news/${this.id}`,
                headers: {
                    access_token: localStorage.getItem('access_token')
                },
                data: {
                    status: this.status
                }
            })
                .then(response => {
                    Vue.$toast.open({
                        message: `News status successfully changed to ${this.status}`,
                        type: 'success',
                    })
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