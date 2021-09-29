<template>
  <section id="dashboard">
        <div id="news-table-data">
            <div class="card title-card">
                <div class="card-body">
                    <h1>News Daily</h1>
                    <strong>Content Management System</strong>
                </div>
            </div>

            <br><br>
            <div class="card">
                <div class="card-body">
                    <button type="button" class="btn btn-primary" style="float: right" @click="changePage('addnews')">Add news</button>
                    <h4>News List</h4>
                    <br>
                    <table class="table">
                        <thead>
                            <tr style="background-color: #5055be">
                                <th scope="col" width="2%">No</th>
                                <th scope="col" width="8%">Title</th>
                                <th scope="col" width="35%">Content</th>
                                <th scope="col" width="11%">Status</th>
                                <th scope="col" width="20%">News Image</th>
                                <th scope="col" width="9%">Author</th>
                                <th scope="col" width="9%">Category</th>
                                <th scope="col" width="9%">Action</th>
                            </tr>
                        </thead>
                        <tbody>

                            <NewsRowData 
                                v-for="(eachNews, index) in news" 
                                :key="eachNews.id" 
                                :eachNews="eachNews"
                                :index="index" 
                                :loggedInUser="loggedInUser" 
                                @editNews="editNews" 
                                @fetchNews="fetchNews('delete')"
                            ></NewsRowData>

                        </tbody>
                    </table>
                </div>
            </div>    
        </div>
    </section>
</template>

<script>
import NewsRowData from '../components/NewsRowData.vue'
import axios from 'axios'

export default {
    name: 'Dashboard',
    data: function() {
        return {
            news: []
        }
    },
    components: {
        NewsRowData
    },
    props: ['loggedInUser'],
    created: function() {
        this.generateFetchingInfo('news')
        this.fetchNews('default')
    },
    methods: {
        changePage(page) {
            this.$emit('changePage', page)
        },
        editNews(payload) {
            this.$emit('editNews', payload)
        },
        fetchNews(type) { // type parameters used to whether show success message or not
            axios({
                method: 'GET',
                url: `${this.baseServerURL}/news`,
                headers: {
                    access_token: localStorage.getItem('access_token')
                }
            })
                .then(response => {
                    if(type !== 'delete') this.generateFetchingSuccess('news') // If detected from delete event, no need to show this message again (too much notification for UX)
                    this.news = response.data
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