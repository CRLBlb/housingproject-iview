<template>
  <Card shadow>
    <div>
      <div class="message-page-con message-category-con">
        <Menu width="auto" active-name="unread" @on-select="handleSelect">
          <MenuItem name="unread">
            <span class="category-title">未读消息</span><Badge style="margin-left: 10px" :count="aGroupNoticeUnreadCount"></Badge>
          </MenuItem>
          <MenuItem name="readed">
            <span class="category-title">所有消息</span><Badge style="margin-left: 10px" class-name="gray-dadge" :count="aGroupNoticeReadedCount"></Badge>
          </MenuItem>
        </Menu>
      </div>
      <div class="message-page-con message-list-con">
        <Menu
          width="auto"
          active-name=""
          :class="titleClass"
          @on-select="handleView"
        >
          <MenuItem v-for="item in noticeList" :name="item.nid" :key="`msg_${item.nid}`">
            <div>
              <p class="msg-title">{{ item.ntitle }}</p>
            </div>
          </MenuItem>
        </Menu>
      </div>
       <div class="message-page-con message-view-con">
        <div class="message-view-header">
          <h2 class="message-view-title">{{ noticeContent.ntitle }}</h2>
          <time class="message-view-time">{{ noticeContent.ndate }}</time>
        </div>
        <div>
          {{noticeContent.ncontent}}
        </div>
      </div>
    </div>
  </Card>
</template>

<script>
import { mapState, mapGetters, mapMutations, mapActions } from 'vuex'
const listDic = {
  unread: 'groupNoticeUnreadList',
  readed: 'groupNoticeReadedList'
}
export default {
  name: 'agent_group',
  data () {
    return {
      currentNoticeType: 'unread',
      noticeContent: [],
      showingNoticeItem: {}
    }
  },
  computed: {
    ...mapState({
      groupNoticeUnreadList: state => state.agent.groupNoticeUnreadList,
      groupNoticeReadedList: state => state.agent.groupNoticeReadedList,
      noticeList () {
        return this[listDic[this.currentNoticeType]]
      },
      titleClass () {
        return {
          'not-unread-list': this.currentNoticeType !== 'unread'
        }
      }
    }),
    ...mapGetters([
      'aGroupNoticeUnreadCount',
      'aGroupNoticeReadedCount'
    ])
  },
  methods: {
    ...mapMutations([
      //
    ]),
    ...mapActions([
      'agentgetContentByGroupId',
      'agentgetUnreadGroupList',
      'agentgetReadGroupList',
      'agentreadGroupNotice'
    ]),
    handleSelect (name) {
      this.currentNoticeType = name
    },
    handleView (nid) {
      this.agentgetContentByGroupId({ nid }).then(content => {
        this.noticeContent = content
        this.agentreadGroupNotice()
        const item = this.noticeList.find(item => item.nid === nid)
        if (item) this.showingNoticeItem = item
      })
    },
    handleReset (name) {
      this.$refs[name].resetFields()
    }
  },
  mounted () {
    // 请求获取消息列表
    this.agentgetUnreadGroupList()
    this.agentgetReadGroupList()
  }
}
</script>

<style lang="less">
.chat-other{
  float: left;
}
.chat-me{
  float: right;
}
.message-page{
  &-con{
    height: ~"calc(100vh - 176px)";
    display: inline-block;
    vertical-align: top;
    position: relative;
    &.message-category-con{
      border-right: 1px solid #e6e6e6;
      width: 200px;
    }
    &.message-list-con{
      border-right: 1px solid #e6e6e6;
      width: 230px;
    }
    &.message-view-con{
      position: absolute;
      left: 446px;
      top: 16px;
      right: 16px;
      bottom: 16px;
      overflow: auto;
      padding: 12px 20px 0;
      .message-view-header{
        margin-bottom: 20px;
        .message-view-title{
          display: inline-block;
        }
        .message-view-time{
          margin-left: 20px;
        }
      }
    }
    .category-title{
      display: inline-block;
      width: 65px;
    }
    .gray-dadge{
      background: gainsboro;
    }
    .not-unread-list{
      .msg-title{
        color: rgb(170, 169, 169);
      }
      .ivu-menu-item{
        .ivu-btn.ivu-btn-text.ivu-btn-small.ivu-btn-icon-only{
          display: none;
        }
        &:hover{
          .ivu-btn.ivu-btn-text.ivu-btn-small.ivu-btn-icon-only{
            display: inline-block;
          }
        }
      }
    }
  }
}
</style>
