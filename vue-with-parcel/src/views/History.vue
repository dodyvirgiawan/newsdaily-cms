<template>
  <section id="history">
        <div id="history-div">
            <div class="card title-card">
                <div class="card-body">
                    <h1>News Daily</h1>
                    <strong>Content Management System</strong>
                </div>
            </div>

            <br><br>
            <div class="card">
                <div class="card-body">
                    <button type="button" class="btn btn-primary" style="float: right" @click="changePage('homepage')">Show news</button>
                    <h4>History List</h4>

                    <br>
                    <table class="table table-borderless table-striped">
                        <thead>
                            <tr style="background-color: #5055be">
                                <th scope="col" width="3%">No</th>
                                <th scope="col" width="15%">News Title</th>
                                <th scope="col" width="30%">Description</th>
                                <th scope="col" width="20%">By</th>
                                <th scope="col" width="12%">Created At</th>
                            </tr>
                        </thead>
                        <tbody>

                            <HistoryRowData 
                                v-for="(history, index) in histories" 
                                :key="history.id" 
                                :historyData="history" 
                                :index="index"
                            ></HistoryRowData>

                        </tbody>
                    </table>
                </div>
            </div>    
        </div>
    </section>
</template>

<script>
import HistoryRowData from '../components/HistoryRowData.vue'
import axios from 'axios'

export default {
    name: 'History',
    data: function() {
        return {
            histories: []
        }
    },
    components: {
        HistoryRowData
    },
    created: function() {
        this.generateFetchingInfo('history')

        axios({
            method: 'GET',
            url: `${this.baseServerURL}/histories`,
            headers: {
                access_token: localStorage.getItem('access_token')
            }
        })
            .then(response => {
                this.generateFetchingSuccess('history')
                this.histories = response.data.histories
            })
            .catch(err => {
                this.generateErrorResponse(err)
            })
    },
    methods: {
        changePage(page) {
            this.$emit('changePage', page)
        }
    }
}
</script>

<style>

</style>