<template>
    <div class="widget border_radius" ref="widget" @scroll="handleScroll">
        <div v-for="item in visiblePosts" :key="item" class="item border_radius">
            <p v-if="item.text != ''">
                {{ item.text }}
            </p>
            
            <div v-if="item.attachments && item.attachments.length > 0" class="imgs">
                <div v-for="photo in item.attachments" :key="photo" class="img">
                    <img v-if="photo.type === 'photo'" :src="photo.photo.sizes[2].url" alt="Фото" class="img border_radius">
                    <img v-if="photo.type === 'link'" 
                    :src="photo.link.photo.sizes[2].url" alt="Фото" class="img border_radius">
                </div>
            </div>
            
            <div class="likes">
                <img src="/img/like.svg" alt="like" class="likeImg">
                <p>{{ item.likes.count }}</p>
            </div>
        </div>
        <div v-if="loading">Loading...</div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    data() {
        return {
            posts: [], // Загруженные посты
            visiblePosts: [], // Посты, отображаемые на текущий момент
            loading: false, // Показывает, загружаются ли новые посты
            offset: 0, // Текущая страница данных
            count: 4, // Количество постов на странице
            maxAttempts: 10,
        }
    },
    mounted() {
        this.getPosts();
    },
    methods: {
        showPosts() {
            for (let i = this.offset; i < this.count + this.offset; i++) {
                if (this.posts.length > i) {
                    this.visiblePosts.push(this.posts[i]);
                }
            }

            this.loading = false;
        },

        async getPosts() {
            // const proxy = "https://cors-anywhere.herokuapp.com/"; // Прокси для обхода CORS
            const proxy = "http://localhost:8010/proxy/";
            const params = {
                access_token: "a2d9b412a2d9b412a2d9b41246a1cf5a14aa2d9a2d9b412c771781abe90d6641ae46af7",
                owner_id: '-73629052',
                offset: this.posts.length,
                count: 20,
                v: "5.131"
            };
            var response = [];
            try {
                response = await axios.get(proxy + '/method/wall.get', {params: params});
            }
            catch (error) {
                console.error(error.message);
                return;
            }

            if (this.posts.length === 0) {
                this.posts = response.data.response.items;
            } else {
                this.posts = this.posts.concat(response.data.response.items);
            }

            this.showPosts();

        },

        handleScroll() {
            const bottomOfPage = this.$refs.widget.scrollTop + this.$refs.widget.clientHeight === this.$refs.widget.scrollHeight;

            if (bottomOfPage && !this.loading || this.$refs.widget.scrollHeight <= this.$refs.widget.clientHeight) {
                this.loading = true;
                this.offset += this.count;
                
                if (this.offset >= this.posts.length) {
                    this.getPosts();
                } else {
                    this.showPosts();
                }   
            }
        },
    },
};
</script>

<style>
    .border_radius {
        border-radius: 5px;
    }
    .widget {
        display: flex;
        flex-direction: column;
        width: 500px;
        height: 500px;
        margin: auto;
        background: #383838;
        padding: 10px;
        color: #fff;

        max-height: 500px;
        overflow-y: auto;
    }

    .item {
        border: 1px solid #5e5e5e;
        border-radius: 3px;
        padding: 5px 20px;
        margin: 0 0 10px 0;
    }

    .imgs {
        display: flex;
        flex-direction: row;
        justify-content: flex-start;
        flex-wrap: wrap;
    }

    .img {
        background-size: contain;
        background-repeat: no-repeat;
        width: fit-content;
        height: 100px;
        margin: 0 10px 10px 0;
    }

    .likes {
        display: flex;
        flex-direction: row;
        justify-items: center;

    }

    .likeImg {
        margin: 0 10px 0 0;
    }
</style>