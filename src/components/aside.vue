<template lang="html">
  <el-card :body-style="bodyStyle" v-if="author.username">
      <div slot="header" class="clearfix">
        <span v-if="hasRecent">作者信息</span>
        <span v-else>个人信息</span>
      </div>
      <div class="text">
          <router-link :to="{name:'user', params: {name: author.id}}" class="author-avatar">
              <img :src="author.avatar_url" alt="" class="img"/>
          </router-link>
          <section class="author-info">
              <router-link :to="{name:'user', params: {name: author.id}}" class="author-name">
                  <span v-text="author.username"></span>
              </router-link>
              <p class="author-detail">
                  <span class="author-score">积分：{{author.score}}</time></span>
              </p>
          </section>
          <!--<section class="author-recent-topic" v-if="hasRecent">
              <header class="title">
                  <span>最近其他文章</span>
              </header>
              <main class="topic-list">
                  <ul>
                      <li v-for="n in recentLength" v-if="author.recent_topics[n-1].id !== topicId">
                          <a href="javascript:" @click.prevent="goToTopic(author.recent_topics[n-1].id)">{{ author.recent_topics[n-1].title }}</a>
                      </li>
                      <li v-if="!author.recent_topics.length">暂无</li>
                      <li v-else>...</li>
                  </ul>
              </main>
          </section>-->
      </div>
    </el-card>
</template>

<script>
export default {
    data() {
        return {
            author: {},
            bodyStyle: {
                display: "-webkit-flex",
                display: "ms-flex",
                display: "flex",
                padding: 0,
                flexDirection: "column",
                position: "relative"
            }
        }
    },
    props: ["authorId", "topicId", "hasRecent"],
    computed: {
        recentLength: function () {
            let length = this.author.recent_topics.length;
            return length > 10 && 10 || length;
        }
    },
    created() {
        if (this.authorId) {
            this.fetchUserInfo();
        }
    },
    mounted() { },
    watch: {
        "authorId"() {
            if (this.authorId) {
                this.fetchUserInfo();
            }
        }
    },
    methods: {
        //获取用户信息
        fetchUserInfo() {
            let self = this;
            $.ajax({
                url: "http://119.23.245.101:8080/User/GetUserid",
                type: "GET",
                data: {
                    id: self.authorId
                }
            }).done((res) => {
                if (!res || !res.success) {
                    //TODO 是否错误抛出  有待商榷
                    return;
                }
                this.author = res.data;
                console.log(this.author)
                this.author.recent_topics = [];
            }).fail((error) => {
                //TODO 是否错误抛出  有待商榷
            });
        },
        goToTopic(topicId) {
            if (!topicId)
                return;
            this.$router.push({
                name: "topic",
                params: {
                    id: topicId
                }
            });
        }
    }
}
</script>

<style lang="css">
.author-avatar {
    max-width: 100%;
    display: block;
    padding: 20px;
    text-align: center;
    border-bottom: 1px solid rgba(160, 160, 160, 0.2);
}

.author-avatar img {
    width: auto;
    max-width: 100%;
    max-height: 100px;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
}

.author-info {
    padding: 20px;
    border-bottom: 1px solid rgba(160, 160, 160, 0.2);
}

.author-name {
    line-height: 48px;
    font-size: 24px;
    font-weight: 300;
    color: #212121;
}

.author-name:hover {
    text-decoration: underline;
}

.author-detail .author-score {
    color: #838383;
}

.author-recent-topic.title {
    padding: 18px 20px;
    border-bottom: 1px solid rgba(160, 160, 160, 0.2);
}




.topic-list ul {
    list-style: none;
}

.topic-list li {
    line-height: 1.5;
    border-bottom: 1px solid rgba(160, 160, 160, 0.2);
    padding: 10px;
}

.topic-list a {
    color: #838383;
}
</style>
