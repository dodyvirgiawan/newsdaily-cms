<template>
  <section id="create-news-page">

        <NewsForm
            :pageName="pageName"
            :newsData="newsData"
            @submitNews="addNews"
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
    name: 'AddNewsForm',
    components: {
        NewsForm
    },
    data: function() {
        return {
            pageName: 'Add News',
            newsData: {
                title: '',
                content: '',
                selectedCategoryId: '',
                imgUrl: '',
                originalImgUrl: ''
            }
        }
    },
    methods: {
        changePage(page) {
            this.$emit('changePage', page)
        },
        addNews(payload) {
            Vue.$toast.open({
                message: 'Uploading news data...',
                type: 'info',
            })

            const form = new FormData()

            form.append('title', payload.title)
            form.append('content', payload.content)
            form.append('categoryId', payload.categoryId)
            form.append('image_path', payload.image_path)
            
            axios({
                method: 'POST',
                url: `${this.baseServerURL}/news`,
                data: form,
                headers: {
                    access_token: localStorage.getItem('access_token')
                }
            })
                .then(response => {
                    swal('Success', 'News has been successfully added', 'success')
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