<template>
    <div>
        <header>
            <div class="left" v-if="!category">
                <img src="@/assets/img/logo.svg" v-if="active==0" />
                <img src="@/assets/img/logo2.svg" v-else />
            </div>
            <div class="left" v-else>
                <span class="category">{{title}}</span>
            </div>
            <div class="center">
                <span :class="{'active':active==index}" @click="select(index)" v-for="(val,index) of nav" :key="val">{{val}}</span>
            </div>
            <div class="right" v-if="!isSearch">
                <svg v-if="!userInfo.userName" class="icon user" aria-hidden="true" @click="$router.push({name:'myBook'})">
                    <use xlink:href="#icon-icon-user"></use>
                </svg>
                <img v-else :src="userInfo.avatar" class="avatar" @click="$router.push({name:'myBook'})" />
                <svg class="icon book" aria-hidden="true" @click="showPop">
                    <use xlink:href="#icon-mulu"></use>
                </svg>
            </div>
            <div class="right" v-else>
                <svg class="icon user search" aria-hidden="true" @click="$router.push({name:'search'})">
                    <use xlink:href="#icon-fenxiang"></use>
                </svg>
                <svg class="icon book search" aria-hidden="true" @click="showPop">
                    <use xlink:href="#icon-mulu"></use>
                </svg>
            </div>
        </header>
        <Popup />
    </div>

</template>

<script>
import Popup from './Popup'
import {
    mixin
} from '@/assets/js/mixins'
export default {
    mixins: [mixin],
    props: {
        active: {
            type: [String, Number],
            default: 0
        },
        category: {
            type: Boolean,
            default: false
        },
        isSearch: {
            type: Boolean,
            default: false
        },
        title: {
            type: String,
            default: '分类'
        },
        nav: {
            type: Array,
            default () {
                return ['男生', '女生']
            }
        }
    },

    data() {
        return {
            showPopFlag: false
        }
    },

    components: {
        Popup
    },

    methods: {
        select(i) {
            this.$emit('select', i)
        },

        left() {
            this.$emit('left')
        },

        showPop() {
            // 防止快速点击
            if (this.showPopFlag) {
                return
            }
            this.showPopFlag = true
            setTimeout(() => {
                this.showPopFlag = false
            }, 300);
            this.setShowTopMenu(!this.showTopMenu)

        }
    },

    watch: {
        showTopMenu(flag) {
            if (flag) {
                document.querySelector('header').style.zIndex = 3000
            } else {
                setTimeout(() => {
                    document.querySelector('header').style.zIndex = 0
                }, 300);
            }
        }
    },
}
</script>

<style lang="scss" scoped>
header {
    padding: 0 15px;
    height: 45px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    position: relative;
    background: #fff;

    div {
        height: 100%;
        display: flex;
        align-items: center;
        flex: 0 0 25%;
    }

    .left {
        img {
            width: 100%;
        }

        .category {
            font-size: 14px;
            margin-left: 5px;
        }

        .icon {
            font-size: 16px;
            padding: 10px 10px 10px 0;
        }
    }

    .center {
        flex: 0 0 50%;
        display: flex;
        justify-content: center;

        span {
            text-align: center;
            padding: 8px 12px;
            color: #ed424b;
            border: 1px solid #ed424b;
            margin-right: -1px;

            &.active {
                color: #fff;
                background: #ed424b;
            }
        }

        span:first-child {
            border-radius: 4px 0 0 4px;
        }

        span:last-child {
            border-radius: 0 4px 4px 0;
        }
    }

    .right {
        display: flex;
        justify-content: flex-end;

        .icon {
            font-size: 20px;
            padding: 5px;
        }

        .user {
            margin-right: 5px;
        }

        .avatar {
            width: 20px;
            height: 20px;
            border-radius: 50%;
            margin-right: 5px;
        }

        .user,
        .book {
            color: #ed424b;
        }

        .search {
            color: #333;
        }
    }
}
</style>
