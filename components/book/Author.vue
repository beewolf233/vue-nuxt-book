<template>
    <div class="author">
        <div class="avatar">
            <img :src="avatar | URL" alt="">
            <span >Lv.4</span>
        </div>
        <div class="author-info">
            <p class="username">{{author}}</p>
            <span class="gray">暂无作者简介</span>
        </div>
        <svg class="icon gray" aria-hidden="true">
            <use xlink:href="#icon-qianjin"></use>
        </svg>
    </div>
</template>

<script>
export default {
    props: {
        avatar: {
            type:String,
            default:''
        },
        author: {
            type:String,
            default:''
        },
    }
}
</script>

<style lang="scss" scoped>
.author {
    display: flex;

    .avatar {
        width: 50px;
        height: 50px;
        flex: 0 0 50px;
        position: relative;
        margin-right: 10px;
        img {
            height: 50px;
            width: 50px;
            border-radius: 50%;
        }

        span {
            position: absolute;
            background: #f0643a;
            border: none;
            left: 2px;
            bottom: 0;
            color: #fff;
            font-style: italic;
            width: 35px;
            height: 10px;
            border-radius: 20px;
            font-size: 12px;
            text-align: center;
            line-height: 10px;
            font-weight: 500;
            padding: 5px;
        }
    }
    .author-info {
        flex: 1;
        display: flex;
        flex-direction: column;
        padding: 5px 0;
        justify-content: space-between;
        .username {
            font-size: 18px;
            font-weight: bold;
        }
    }
    .icon {
        flex: 0 0 20px;
        height: 50px;
    }
}
</style>
