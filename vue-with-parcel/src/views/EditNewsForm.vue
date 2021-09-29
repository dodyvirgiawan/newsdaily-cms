<template>
  <section id="edit-news-page">

        <NewsForm
            :pageName="pageName"
            :newsData="newsData"
            @submitNews="editNews"
            @changePage="changePage"
        ></NewsForm>

    </section>
</template>

<script>
import axios from 'axios'
import NewsForm from '../components/NewsForm.vue'
import Vue from 'vue'
import VueToast from 'vue-toast-notification'
import 'vue-toast-notification/dist/theme-sugar.css';

Vue.use(VueToast, {position: 'top-right'}) // Plugins

export default {
    name: 'EditNewsForm',
    components: {
        NewsForm
    },
    data: function() {
        return {
            pageName: 'Edit News',
            newsData: {
                title: '',
                content: '',
                selectedCategoryId: '',
                imgUrl: '',
                originalImgUrl: ''
            }
        }
    },
    props: ['editedNewsId'],
    created: function() {
        this.generateFetchingInfo('news')

        axios({
            method: 'GET',
            url: `${this.baseServerURL}/news/${this.editedNewsId}`,
            headers: {
                access_token: localStorage.getItem('access_token')
            }
        })
            .then(response => {
                this.newsData.title = response.data.title
                this.newsData.content = response.data.content
                this.newsData.selectedCategoryId = response.data.Category.id
                this.newsData.imgUrl = response.data.imgUrl
                this.newsData.originalImgUrl = response.data.imgUrl
            })
            .catch(err => {
                this.generateErrorResponse(err)
            })
    },
    methods: {
        changePage(page) {
            this.$emit('changePage', page)
        },
        editNews(payload) {
            Vue.$toast.open({
                message: 'Uploading edited news data...',
                type: 'info',
            })

            const form = new FormData()

            form.append('title', payload.title)
            form.append('content', payload.content)
            form.append('categoryId', payload.categoryId)
            form.append('image_path', payload.image_path)

            axios({
                method: 'PUT',
                url: `${this.baseServerURL}/news/${this.editedNewsId}`,
                data: form,
                headers: {
                    access_token: localStorage.getItem('access_token')
                }
            })  
                .then(response => {
                    swal('Success', 'News has been successfully edited', 'success')
                    this.newsData = {}
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