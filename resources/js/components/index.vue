<template>
    <div class="section columns is-centered">
        <div class="column is-one-quarter">
            <div class="field has-addons">
                <div class="control is-expanded">
                    <input @keydown.enter="create" v-model="name" autofocus class="input" type="text" placeholder="新表单">
                </div>
                <div class="control">
                    <a @click="create" :class="{'button is-info': true, 'is-loading': loading}">
                        确定
                    </a>
                </div>
            </div>

            <nav v-if="forms && forms.length > 0" class="panel">
                <router-link :to='{name: "recycle", params: {id: item.id}}' v-for="item in forms" :key="item.id" :class="{
                    'panel-block': true,
                    'is-active': item.record_count > 0
                }">
                    <span class="panel-icon">
                        {{ item.record_count }}
                    </span>
                    {{ item.name }}
                </router-link>
            </nav>

            <div class="content has-text-centered">
                <p>Copyright &copy; {{ year }} 小可爱</p>
                <p>项目在 <a href="https://github.com/ss098/work">GitHub</a> 提供开放源代码版本</p>
            </div>

        </div>
    </div>
</template>
<style scoped>
    .field {
        user-select: none;
    }
</style>
<script>
    import swal from "sweetalert"

    export default {
        data: () => {
            return {
                name: "",
                loading: false,
                forms_loading: false,
                forms: []
            }
        },
        methods: {
            create: function () {
                if (!this.loading) {
                    const name = this.name;
                    if (name) {
                        this.loading = true;

                        axios.post("/create", {
                            name: name
                        }).then(response => {
                            this.loading = false;

                            this.$router.push({name: "recycle", params: {id: response.data.id}})
                        }).catch(error => {
                            swal({
                                text: "创建表单失败",
                                icon: "error",
                                timer: 2000
                            })

                            this.loading = false
                        })
                    } else {
                        swal({
                            text: "请输入表单名称",
                            timer: 2000
                        })
                    }
                }
            },
            get_forms: function () {
                this.forms_loading = true;
                axios.get("/all").then(response => {
                    if (response.data.success) {
                        this.forms = response.data.forms;
                        this.forms_loading = false
                    } else {
                        this.forms_loading = false
                    }
                })
            }
        },
        computed: {
            year: function () {
                let date = new Date();

                return date.getFullYear()
            }
        },
        mounted: function () {
            this.get_forms()
        }
    }
</script>
