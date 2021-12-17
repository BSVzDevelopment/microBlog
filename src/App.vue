<template>
    <div class="app">
        <form @submit.prevent>
            <h3>Создание поста</h3>
            <input
                    v-bind:value="title"
                    @input="title = $event.target.value"
                    class="input"
                    type="text"
                    placeholder="Введите название">
            <input
                    v-bind:value="body"
                    @input="body = $event.target.value"
                    class="input"
                    type="text"
                    placeholder="Добавьте описание.я">

            <button class="btn" v-on:click="createPost">Создать</button>
        </form>
        <div v-if="posts.length !==0">

            <transition-group name="post-list">!!!
                <!-- <div class="post" v-for="(post, idx) in posts">
                    <div> ({{ idx + 1}}) <strong>Название:</strong> {{ post.title }}</div>
                    <div><strong>Краткое описание:</strong> {{ post.body }}</div>
                    <div><strong>Комментарии:</strong> {{ post.comment }}</div>
                    <div>
                        <div>
                            <button class="postBtn" @click="removePost(idx)">Удалить</button>
                        </div>
                    </div>
                </div> -->
            </transition-group>
        </div>
        <div v-if="posts.length === 0" class="allert">Записей пока нет</div>
        <div class="observer" ref="observer"></div>
        <!--        <div class="page__wrapper">-->
        <div
                :key="pageNumber"
                class="page"
                :class=" {
                     'current-page' : page === pageNumber
                 } "
        > {{ pageNumber }} </div>
        <!--        </div>-->
    </div>
</template>

<script>
    import axios from 'axios'

    export default {
        data() {
            return {
                posts: [ ],
                title: "",
                body: "",
                page: 1,
                limit: 10,
                totalPages: 0,
            }
        },

        methods: {
            createPost() {
                const newPost = {
                    id: Date.now(),
                    title: this.title,
                    body: this.body,
                }

                this.posts.push(newPost);
                this.title = "";
                this.body = "";


            },

            removePost(idx) {
                this.posts.splice(idx, 1)
            },

            async fetchPosts() {
                try {
                    this.page += 1;
                    const response = await axios.get('https://jsonplaceholder.typicode.com/posts', {
                        params: {
                            _page: this.page,
                            _limit: this.limit
                        }
                    });
                    this.totalPages = Math.ceil(response.headers['x-total-count']/this.limit)
                    this.posts = [... this.posts, ... response.data];

                } catch (e) {
                    alert('Ошибка')
                }
            }
        },
        mounted() {
            this.fetchPosts();
            // console.log( this.$refs.observer)
            const options = {
                rootMargin: '0px',
                threshold: 1.0
            }
            const callback = (entries, observer) => {
                // if (entries[0].isIntersecting && this.page < this.totalPages) {
                if (entries[0].isIntersecting ) {
                    this.fetchPosts()
                }
            };
            const observer = new IntersectionObserver(callback, options);
            observer.observe(this.$refs.observer);
        }
    }
</script>

<style>
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }

    .app {
        padding: 20px;
    }

    .post {
        padding: 15px;
        border: 2px solid #22a6bf;
        margin-top: 15px;
        /*display: flex;*/
        /*align-items: center;*/
        /*justify-content: space-between;*/

    }

    .postBtn {
        margin-top: 10px;
        align-self: flex-end;
        padding: 10px 15px;
        background: none;
        color: darkred;
        border: 1px solid darkred;


    }

    form {
        display: flex;
        flex-direction: column;
    }

    .input {
        width: 100%;
        border: 1px solid #22a6bf;
        padding: 10px 15px;
        margin-top: 10px;
    }

    .btn {
        margin-top: 15px;
        align-self: flex-end;
        padding: 10px 15px;
        background: none;
        color: teal;
        border: 1px solid teal;
    }
    .allert {
        text-align: center;
    }

    .post-list-item {
        display: inline-block;
        margin-right: 10px;
    }
    .post-list-enter-active,
    .post-list-leave-active {
        transition: all 1s ease;
    }
    .post-list-enter-from,
    .post-list-leave-to {
        opacity: 0;
        transform: translateY(30px);
    }

    .page__wrapper {
        display: flex;
        margin-top: 15px;
    }

    .page {
        border: 1px solid black;
        padding: 10px;
    }

    .current-page {
        border: 2px solid teal;
    }

    .observer {
        height: 30px;
        background: #22a6bf;
    }
</style>