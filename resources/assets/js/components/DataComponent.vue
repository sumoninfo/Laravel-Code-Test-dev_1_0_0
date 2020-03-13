<template>

    <div class="col-md-12">
        <div class="row">
            <div class="col-lg-3">
                <input class="form-control" placeholder="Search here..." type="text" v-model="keywords">

            </div>
            <div class="col-lg-3">
                <select v-model="group_filter" class="form-control">
                    <option value="">Please Select...</option>
                    <option v-for="group in groupData" :value="group.type">
                        {{group.type.toUpperCase()}}
                    </option>
                </select>
            </div>
            <div class="col-lg-3">
                <datepicker v-model="date_search" name="birthdate"></datepicker>
            </div>

        </div>
        <hr>
        <table class="table table-hover table-bordered social-accounts">
            <thead>
            <tr>
                <th>Group Name</th>
                <th>Group Type</th>
                <th>Account Name</th>
                <th>Post Text</th>
                <th>Time</th>
            </tr>
            </thead>
            <tbody>

            <tr v-for="history in historyData.data" :key="history.id">
                <td>{{ history.group.name }}</td>
                <td>{{ history.group.type }}</td>
                <td>
                    <div class="media">
                        <div class="media-left">
                            <a href="">
                                 <img width="50" class="media-object img-circle"
                                     v-bind:src="history.account.avatar"/>
                            </a>
                        </div>
                        <div class="media-body media-middle" style="width: 180px;">
                            <h4 class="media-heading">{{ history.account.name }}</h4>
                        </div>
                    </div>
                </td>
                <td>{{ history.post_text }}</td>
                <td>{{ history.created_at }}</td>
            </tr>
            </tbody>
        </table>
        <pagination :data="historyData" @pagination-change-page="getResults"></pagination>
    </div>
</template>

<script type="application/javascript">
    import Datepicker from 'vuejs-datepicker'
    export default {
        components: {
            Datepicker
        },
        mounted() {
            console.log('Component mounted.')
        },
        data() {
            return {
                historyData: {},
                groupData: {},
                keywords: null,
                group_filter: null,
                date_search: null,
            }
        },
        created() {
            this.getResults();
        },
        watch: {
            keywords(after, before) {
                this.fetch();
            },
            group_filter(after, before) {
                this.fetch();
            },
            date_search(after, before) {
                this.fetch();
            }
        },
        methods: {
            fetch() {
                axios.get('/api/search', {params: {keywords: this.keywords, group_filter: this.group_filter, date_search: this.date_search}})
                    .then(response => this.historyData = response.data)
                    .catch(error => {
                    });
            },
            getResults(page) {
                if (typeof page === 'undefined') {
                    page = 1;
                }

                axios.get('/historydata?page=' + page)
                    .then(response => this.historyData = response.data)
                    .catch(error => {
                    });
                axios.get('/groupData')
                    .then(response => this.groupData = response.data)
                    .catch(error => {
                    });
            }
        }
    }
</script>