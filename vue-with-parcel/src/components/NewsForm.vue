<template>
   <div class="page-heading">
        <div id="news-div">
            <br>
            <div class="card title-card">
                <div class="card-body">
                    <h1>News Daily</h1>
                    <strong>Content Management System</strong>
                </div>
            </div>
            
            <br>
            <button type="button" class="btn btn-primary" style="float: right" @click="changePage('homepage')">Show News List</button><br><br><br>
            <div class="card">
                <div class="card-header">
                    {{ pageName }}
                </div>
                <div class="card-body">
                    <form id="news-form">
                        <div class="form-group">
                            <label for="news-title">News title</label>
                            <input class="form-control" type="text" id="news-title" placeholder="News title" v-model="news.title">
                            <small>This is the title of the news.</small>
                        </div>
                        <div class="form-group">
                            <label for="news-content">News content</label>
                            <textarea class="form-control" id="news-content" v-model="news.content"></textarea>
                            <small>Input the content of the news.</small>
                        </div>
                        <div class="form-group">
                            <label for="news-category">Categories</label>
                            <div id="category-selection">
                                <select class="form-control" v-model="news.selectedCategoryId">

                                    <option 
                                        v-for="categoryData in fetchedCategories" 
                                        :key="categoryData.id" 
                                        :value="categoryData.id"
                                    >{{ categoryData.name }}</option>

                                </select>
                            </div>
                            <small>Please select the category which the news belongs to.</small>
                        </div>

                        <div class="form-group">
                            <label for="news-image">Image</label><br>
                            <div id="uploaded-image-section">
                                <div v-if="news.imgUrl">
                                    <br>
                                    <img id="uploaded-image" :src="news.imgUrl" alt="Uploaded image" style="max-width: 20%; height: auto;">
                                    <br><br>
                                </div>
                            </div>
                            <input type="file" ref="image" class="form-control-file" id="news-image" @change="selectFile">
                        </div><br>

                        <button type="button" class="btn btn-primary btn-block" @click="submitNews">Submit</button>
                    </form><br>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'

export default {
    name: 'NewsForm',
    props: ['pageName', 'newsData'],
    data: function() {
        return {
            news: {},
            fetchedCategories: []
        }
    },
    methods: {
        changePage(page) {
            this.$emit('changePage', page)
        },
        selectFile() {
            const file = this.$refs.image.files[0]
            file ? this.news.imgUrl = URL.createObjectURL(file) : this.news.imgUrl = this.newsData.originalImgUrl
        },
        submitNews() {
            const payload = {
                title: this.news.title,
                content: this.news.content,
                categoryId: this.news.selectedCategoryId,
                image_path: this.$refs.image.files[0]
            }

            this.$emit('submitNews', payload)
        },
    },
    created: function() {
        this.news = this.newsData

        axios({
            method: 'GET',
            url: `${this.baseServerURL}/categories`,
            headers: {
                access_token: localStorage.getItem('access_token')
            }
        })
            .then(response => {
                this.fetchedCategories = response.data.categories
                if(this.pageName === 'Add News') this.news.selectedCategoryId = response.data.categories[0].id
            })
            .catch(err => {
                this.generateErrorResponse(err)
            })
    }
}
</script>

<style>

</style>