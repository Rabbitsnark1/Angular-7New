<template>
    <div>
        <h1>Страница с постами</h1>
        <!-- <my-input
            v-model="searchQuery"
            placeholder="Поиск..."
            v-focus
    /> -->
    <div class="app__btns">
        <my-button @click="showDialog">
            Создать пост
        </my-button>
       <!-- <my-select
       v-model="selectedSort"
       :options="sortOptionts"
       /> -->
    </div>
    <my-dialog v-model:show="dialogVisible">
        <post-form
            @create="createPost"
        />
    </my-dialog>
    <post-list
        :posts="sortedAndSearchedPosts"
        @remove="removePost"
        v-if="!isPostsLoading"
        />
    <div v-else>Идёт загрузка...</div>    
    <div v-intersection="loadMorePosts" class="observer"></div>
    </div>
</template>
<script>
import PostForm from "@/components/PostForm.vue";
import PostList from "@/components/PostList.vue";
import MyDialog from "@/components/UI/MyDialog.vue";
import MyButton from "@/components/UI/MyButton.vue";
import axios from "axios";
import MySelect from "@/components/UI/MySelect.vue";
import MyInput from "@/components/UI/MyInput.vue";
import {mapState, mapMutations, mapActions, mapGetters} from 'vuex';
export default {
    components: {
        MyInput,
        MySelect,
        MyButton,
        MyDialog,
        PostList, PostForm      
    },
    data() {
        return {
         dialogVisible: false,
        }
    },
    methods: {
        ...mapMutations ({
            setPage: 'post/setPage'
        }),
        ...mapActions({
            loadMorePosts: 'post/loadMorePosts',
            fetchPosts: 'post/fetchPosts'
        }),
        createPost(post) {
            this.posts.push(post);
            this.dialogVisible= false;
        },
        removePost(post) {
            this.posts = this.posts.filter(p => p.id !== post.id)
        },
        showDialog() {
            this.dialogVisible = true;
        },
        async fetchPosts() {
            try {
                this.isPostsLoading = true;
                const response = await axios.get('https://jsonplaceholder.typicode.com/posts', {
                    params: {
                        _page: this.page,
                        _limit: this.limit
                    }
                });
                this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
                this.posts = response.date;
            } catch (e) {
                alert('Ошибка')
            } finally {
                this.isPostsLoading = false;
            }
        },
        async LoadMorePosts() {
            try {
                this.page += 1;
                const response = await axios.get('https://jsonplaceholder.typicode.com/posts', {
                    params: {
                        _page: this.page,
                        _limit: this.limit
                    }
                });
                this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
                this.posts = [...this.posts, ...response.data];
            } catch (e) {
                alert('Ошибка')
            }
        }
    },
    
    mounted() {
        // this.fetchPosts()
    //     const options = {
    //     rootMargin: '0px',
    //     threshold: 1.0
    // }
    //  const callback = (entries, observer) => {
    //     if(entries[0].isIntersecting && this.page <script this.totalPages) {
    //         this.LoadMorePosts()
    //     }
    //  };
    //  const observer = new IntersectionObserver(callback, options);
    //  observer.observer(this.$refs.observer);
    // },
        },

    computed: {
        ...mapState({
            posts: state => state.post.post,
            isPostsLoading: state => state.post.isPostsLoading,
            selectSort: state => state.post.selectedSort,
            seachQuery: state => state.post.seachQuery,
            page: state => state.post.page,
            limit: state => state.post.limit,
            totalPages: state => state.post.totalPages,
            sortOptionts: state => state.post.sortOptionts
        }),
        ...mapGetters({
            sortedPosts: 'post/sortedPosts',
            sortedAndSearchedPosts: 'post/sortedAndSearchedPosts'
        })
    },
    watch: {
    }
    
}