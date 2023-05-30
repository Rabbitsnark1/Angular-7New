<template>
    <div v-show="posts.length > 0">
        <h3>Список пользователей</h3>
        <transition-group name="post-list">
            <post-item
                v-for="post in posts"
                :post="post"
                :key="post.id"
                @remove="$emit('remove', post)"
            />      
        </transition-group>
    </div>
    <post-item
        v-for="post in posts"
        "post="post"
        :key="post.id"
        @remove="$emit('remove', post)"
    />    
    </div>
    <h2 v-show="posts.length === 0" style="color: crimson">
      Список постов пуст.
    </h2>
</template>

<script>
import PostItem from "@/components/PostItem.vue";
export default {
    components: {PostItem},
    props: {
        posts: {
            type: Array,
            required: true
        }
    }
}
</script>

<style scoped>
.post-list-item {
    display: inline-block;
    margin-right: 10px;
}
.post-list-enter-active,
.post-list-leave-active {
    transition: all 0.4s ease;
}
.post-list-enter-from,
.post-list-leave-to {
    opacity: 0;
    tranform: translateX(130px);
}
.post-list-move {
    transition: tranform 0.4s ease;
}
</style>