<template>
  <div class="wrapper">
    <h1 class="title">
      {{title}}
      <span class="num">{{total}}</span>
    </h1>
    <div class="item border-bottom"
         @click="showMenu(item)"
         v-for="(item, index) in comments"
         :key="index">
      <div class="left-img">
        <img :src="item.user.avatarUrl + '?param=50y50'"
             alt />
      </div>
      <div class="right-info">
        <div class="top-info">
          <div class="data">
            <div class="nickname">{{item.user.nickname}}</div>
            <div class="time">{{ item.time | setTime}}</div>
          </div>
          <div class="like"
               :class="{liked: item.liked}"
               @click.stop="likeThisComment(item.commentId, item)">
            <span v-show="item.likedCount > 0">{{item.likedCount}}</span>
            <i class="comment"
               :class="{ commentzan: !item.liked, commentzan1:item.liked}"></i>
          </div>
        </div>
        <!-- 这里使用 v-html 用来解析 当出现 \n 换行的时候插入 <br />标签 -->
        <div class="content"
             v-html="setCon(item.content)"></div>
      </div>
    </div>
  </div>
</template>

<script>
import { filterSetMonth } from 'utils/filters'
import Bus from '@/assets/Bus'
export default {
  name: '',
  props: {
    total: {
      type: Number
    },
    comments: {
      type: Array
    },
    title: {
      type: String
    }
  },
  filters: {
    setTime: function (val) {
      return filterSetMonth(val, '月', '日')
    }
  },
  methods: {
    likeThisComment (cid, item) {
      item.liked = !item.liked
      if (item.liked) {
        item.likedCount++
      } else {
        item.likedCount--
      }
      this.$emit('likeComment', cid, item.liked)
    },
    showMenu (item) {
      console.log(item)
      const localUserId = +localStorage.getItem('accountUid')
      const userId = item.user.userId
      if (localUserId === userId) {
        // 当前评论是自己的，可以删除，不能举报
        Bus.$emit('user', true)
      }
      Bus.$emit('comId', item.commentId)
      this.$emit('showMenu')
    },
    /**
     * 将返回来的数据进行格式化处理
     * 将 换行符\n 替换成 <br /> 实现换行
     * 将 回车符\r\n 替换成 <br /> 实现换行
     */
    setCon (val) {
      val = val.replace(/(\n)|(\r\n)/g, '<br />')
      return val
    }
  }
}
</script>

<style lang='less' scoped>
@import url("//at.alicdn.com/t/font_1478463_b9awmkqysf8.css");
@keyframes like {
  50% {
    transform: scale(1.2);
  }
  100% {
    transform: scale(1);
  }
}
.wrapper {
  margin-top: 0.3rem;
}
.title {
  font-weight: 700;
}
.item {
  display: flex;
  box-sizing: border-box;
  margin: 0.3rem 0;
  padding-bottom: 0.3rem;
  height: auto;
  .left-img {
    width: 0.6rem;
    height: 0;
    padding-bottom: 0.6rem;
    margin-right: 0.2rem;
    img {
      width: 0.6rem;
      height: 0.6rem;
      border-radius: 50%;
    }
  }
  .right-info {
    width: 100%;
    .top-info {
      color: #999;
      line-height: 1.3;
      font-size: 0.24rem;
      .flex-between();
      margin-bottom: 0.13rem;
      .data {
        .time {
          font-size: 0.21rem;
        }
      }
      .liked {
        animation: like 0.7s ease 1;
        color: @bgcolor;
      }
    }
    .content {
      line-height: 1.5;
    }
  }
}
</style>
