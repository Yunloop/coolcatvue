<template>
  <div class="main_wrap">
    <div v-for="(article,index) in article_list"
         :key="index"
         class="article">
      <div class="cover">
        <img :src="url + article.cover_image"
             v-if="article.cover_image"
             @click="articleDetail(article.id)" />
        <img src="static/images/cover_article.jpg"
             v-else
             @click="articleDetail(article.id)" />
      </div>
      <div class="intro">
        <p>{{ article.pub_time | FromNow }}</p>
        <p class="title"
           @click="articleDetail(article.id)">{{ article.title }}</p>
        <p>
          <label class="category">分类：{{ article.category.name }}</label>
          <label class="tag"
                 v-for="(tag,index) in article.tags"
                 :key="index">{{ tag.name }} </label>
        </p>
        <p><span class="edit"
                @click="articleEdit(article.id)">编辑</span></p>
      </div>
    </div>
  </div>
</template>

<script>
import cons from '@/components/constent'
import moment from 'moment'
import 'moment/locale/zh-cn'
let uid = localStorage.uid
let token = localStorage.token

export default {
  components: {
  },
  created () {
    this.authenticate()
    this.getArticles()
  },
  data () {
    return {
      article_list: [],
      url: cons.apis
    }
  },
  filters: {
    FromNow: function (val) {
      return moment(val).format('YYYY年MM月DD日 A h:mm:ss')
      // return moment(val).fromNow();
    }
  },
  methods: {
    // 验证是否登录
    authenticate () {
      if (!(uid && token)) {
        alert('请先确认您已登录！')
        this.$router.push({ path: '/' })
      }
    },
    getArticles () {
      if (uid) {
        this.axios.get(cons.apis + 'api/articles/', {
          params: { 'author': uid }
        }).then(response => {
          this.article_list = response.data
        }).catch(() => {
          alert('获取文章数据失败！')
        })
      }
    },
    // 去详情页传递参数
    articleDetail (id) {
      let newpage = this.$router.resolve({
        name: 'Article'
      })
      sessionStorage.setItem('id', JSON.stringify(id))
      window.open(newpage.href, '_blank')
    },
    // 去编辑页传参
    articleEdit (id) {
      this.$router.push({
        name: 'ArticleEdit',
        params: {
          id: id
        }
      })
    }
  }
}
</script>

<style scoped>
.main_wrap {
  margin: 2rem 30rem;
}
.article {
  min-height: 12rem;
  font-size: 0;
  background: #ffffff;
  margin-bottom: 0.2rem;
}
.article .intro {
  display: inline-block;
  width: 50%;
  padding: 4% 0;
}
.article .intro p {
  color: gray;
  margin: 1% auto;
  font-size: 1.3rem;
  cursor: default;
}
.article .intro .title {
  font-size: 2rem;
  color: black;
  font-weight: bold;
  cursor: pointer;
}
.article .intro .title:hover {
  color: dodgerblue;
}
.article .intro .category {
  font-weight: 600;
}
.article .intro .edit {
  font-weight: 100;
  cursor: pointer;
  text-decoration: underline;
  color: dodgerblue;
}
.article .cover {
  display: inline-block;
  vertical-align: top;
  width: 25%;
  padding: 4% 0 4% 25%;
}
.article .cover img {
  height: 10rem;
  cursor: pointer;
}
@media screen and (max-width: 700px) {
  .main_wrap {
    margin: 1.5rem 0;
  }
  .article .intro {
    width: 60%;
    /* background: teal; */
  }
  .article .intro p {
    padding: 0 3rem 0 2rem;
  }
  .article .intro .edit {
    padding-right: 3rem;
    float: right;
  }
  .article .cover {
    width: 40%;
    padding: 5% 0;
    /* background: tomato; */
  }
  .article .cover img {
    width: 15rem;
    height: auto;
    padding-left: 3rem;
  }
}
</style>
