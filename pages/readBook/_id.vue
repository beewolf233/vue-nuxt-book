<template>
    <div class="read-warp">
        <!-- 顶部title -->
        <Title/>
        <!-- 底部菜单 -->
        <Menu/>
        <!-- 更多 -->
        <More/>
        <!-- 左边侧滑目录 -->
        <Slider @chapter='chapter' :title="title"/>
        <!-- 进度条 -->
        <Progress :title="title" @change='change' :chapterCount='chapterCount' @prev='prev' @next='loadMore'/>
        <!-- 设置主题 -->
        <Theme/>
        <!-- 设置字体 -->
        <Font/>
        <!-- 文章内容 -->
        <Reader :content='content' :title='title' @loadMore='loadMore' :chapterCount='chapterCount' @prev='prev'/>
    </div>
</template>

<script>
import Title from '@/components/readBook/Title'
import Menu from '@/components/readBook/Menu'
import Theme from '@/components/readBook/Theme'
import Font from '@/components/readBook/Font'
import Reader from '@/components/readBook/Reader'
import More from '@/components/readBook/More'
import Progress from '@/components/readBook/Progress'
import Slider from '@/components/readBook/Slider'
import * as types from '@/store/mutations-type'
import Chapter from "@/components/public/Chapter"
import {mixin} from '@/assets/js/mixins'
export default {
    mixins:[mixin],
    async asyncData({$axios,store,params}) {
        const id = params.id
        const bookAtoc = await $axios.$get(`/zhuishu/atoc?view=summary&book=${id}`)     // 这个是书籍源
        const book_id = bookAtoc[0]._id
        const [data,data1,data2] = await Promise.all([
            $axios.$get(`/api/book?id=${id}`),
            $axios.$get(`/api/chapters?id=${book_id}`),
            $axios.$get(`/api/getBookOne?id=${id}`),
        ])

        if (data.code == 10000 && data1.code == 10000 && data2.code == 10000) {
            let book = data.book
            book.catalog = data1.data   // 章节目录
            store.commit(types.BOOKREAD, book)
            return {
                title: data2.data.book&&data2.data.book.readChapter || data1.data[0].title,
                chapterCount: data2.data.book&&data2.data.book.readChapterIndex || 0,
                isCollection: data2.data.book ? true : false
            }
        }
    },

    data() {
        return {
            chapterCount:0, // 当前章节
            content: [], // 章节内容
            isCollection: false,    // 用来判断该书是否已经添加到我的书架，false为没有添加
            title:'',
            locked: false
        }
    },
    components: {
        Title,
        Menu,
        More,
        Chapter,
        Slider,
        Progress,
        Theme,
        Font,
        Reader,
    },

    mounted () {
        this.getBookContent()
    },

   
    beforeRouteLeave (to, from, next) {
        this.setMenuVisible(false)
        this.setSettingVisible(-1)
        next()
    },

    methods: {
        chapter(index) {
            this.chapterCount = index
            this.getBookContent(index)
        },
        async getBookContent(index=this.chapterCount) {
            // 默认获取第一章节的内容
            if (this.locked) {
                return
            }
            this.locked = true
            this.content = []
            this.title = ''
            this.$toast.loading({
                mask: true,
                message: '加载中...'
            });
            const data = await this.$axios.$get(`/chapter/`+encodeURIComponent(this.bookRead.catalog[index].link))
            if (data.ok) {
                this.locked = false
                this.$toast.clear()
                if (data.chapter.isVip) {   // VIP章节,暂未处理
                    this.content = 'vip章节，暂未找到合适的源'
                } else {
                    this.content = data.chapter.cpContent.split(/\n/)
                }
                this.title = this.bookRead.catalog[index].title
                if (this.userInfo.userName&&this.isCollection) {
                    this.postBook(index)
                }
                
            }
        },

        // 把阅读的记录加到服务器
        async postBook(index) {
            const data = await this.$axios.$post(`/api/postBook`,{
                id: this.$route.params.id,
                readChapter: this.title,
                readChapterIndex: index
            })
        },

        // 加载下一页
        async loadMore() {
            // 最后一页
            if (this.locked) {
                return
            }
            this.chapterCount += 1
            this.getBookContent(this.chapterCount)
        },

        // 把阅读的记录增加的本地缓存
        async prev() {
            if (this.locked) {
                return
            }
            this.chapterCount -= 1
            this.getBookContent(this.chapterCount)
        },

        change(e) {
            this.chapterCount = Number(e)
            this.getBookContent(e)
        },

        
    }


}
</script>



