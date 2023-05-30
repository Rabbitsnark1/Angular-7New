<template>
    <div class="app">
        <div>
            <div>{{ post.id  }}</div>
            <div><strong>Название:</strong> {{ post.title }}</div>
            <div><strong>Описание:</strong> {{ post.title }}</div>
        </div>    
      <post-form/>
      <post-list 
        :posts="sortedAndSearched"
        @remove="removePost"
        v-if="!isPostsLoading"
        />
        <div v-else>Идёт загрузка</div>
        <div class="page__wrapper">
            <div 
            v-for="page in totalPages" 
            :key="page"
            class="page"
            :class="{
                'current-page': page === pageNumber
            }"
            @click="changePage(pageNumber)"
        >{{ pageNumber  }}</div>
        </div>
    </div>  
</template>

<script>
import PostForm from "@/components/PostForm.vue";
import PostList from "@/components/PostList.vue";
import MyDialog from "@/components/UI/MyDialog.vue";
import MyButton from "@/components/UI/MyButton.vue";
import axios from "axios";
export default {
    components: {
        MySelect,
        MyButton,
        MyDialog,
        PostList, PostForm      
    },
    data() {
        return {
            posts: [],
            dialogVisible: false,
            isPostsLoading: false,
            selectedSort: '',
            searchQuery: '',
            page: 1,
            limit: 10,
            totalPages: 0,
            sortOptionts: [
                {value: 'title', name: 'По названию'},
                {value: 'body', name: 'По содержанию'},
            ]
        }
    },
    methods: {
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
        this.fetchPosts()
        const options = {
        rootMargin: '0px',
        threshold: 1.0
    }
     const callback = (entries, observer) => {
        if(entries[0].isIntersecting && this.page < this.totalPages) {
            this.LoadMorePosts()
        }
     };
     const observer = new IntersectionObserver(callback, options);
     observer.observer(this.$refs.observer);
    },
    computed: {
        sortedPosts() {
            return [...this.posts].sort((post1, post2) => {
                return post1[this.selectedSort]?.localCompare(post2[this.selectedSort])
            })
        },
        sortedAndSearched() {
            return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
        }
    },
    watch: {
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

.app_btns {
    margin: 15px 0;
    display: flex;
    justify-content: space-between;
}

.page__wrapper {
    display: flex;
    margin-top: 15px;
}

.page {
    border: 2px solid thistle;
    padding: 10 px;
    margin: 3 px;
}

.current-page {
    border: 3px solid blueviolet;
}

.observer {
    height: 30px;
    background: bisque;
}

</style>