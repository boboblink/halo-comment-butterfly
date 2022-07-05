<template>
  <section
    :id="respondId"
    class="comment-editor"
    role="form"
    ref="editor"
    v-if="isCurrReply"
  >
    <form class="comment-form">
      <div class="author-info">
        <!-- 用户头像信息 -->
        <div class="commentator">
          <img :src="avatar" class="avatar" @error="handleAvatarError" />
          <div class="socila-check" :class="[checkType.back]">
            <i :class="[checkType.icon]" aria-hidden="true"></i>
          </div>
        </div>
        <!-- :popupText="configs.authorPopup || '输入QQ号将自动拉取昵称和头像'" -->
        <PopupInput
          class="cmt-popup cmt-author"
          popupStyle="margin-left: -115px"
          :popupText="configs.authorPopup"
          inputText="昵称"
          inputType="text"
          placeholder="必填"
          id="author"
          v-model="comment.author"
          @blurInput="pullInfo"
        />
        <PopupInput
          class="cmt-popup"
          popupStyle="margin-left: -65px;"
          :popupText="configs.emailPopup"
          inputText="邮箱"
          inputType="text"
          placeholder="必填"
          id="email"
          v-model="comment.email"
          @blurInput="pullInfo"
        />
        <PopupInput
          class="cmt-popup"
          popupStyle="margin-left: -55px;"
          :popupText="configs.urlPopup || '禁止小广告😀'"
          inputText="个人站点"
          inputType="text"
          placeholder="选填"
          id="url"
          v-model="comment.authorUrl"
        />
      </div>
      <div class="comment-textarea" v-if="!previewMode">
        <textarea
          required="required"
          aria-required="true"
          tabindex="4"
          :placeholder="configs.aWord || '你是我一生只会遇见一次的惊喜 ...'"
          v-model="comment.content"
          class="commentbody"
        ></textarea>
        <label class="input-label">{{
          configs.aWord || "你是我一生只会遇见一次的惊喜 ..."
        }}</label>
      </div>
      <div
        class="comment-preview markdown-body"
        v-else
        v-html="renderedContent"
      ></div>
      <!-- 上传图片预览 -->
      <div id="upload-img-show"></div>
      <!-- 表情开关 -->
      <div class="comment-tools">
        <p id="emotion-toggle" class="no-select">
          <span v-if="!emojiDialogVisible" @click="handleToggleDialogEmoji">
            <svg t="1656983622616" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="2570" width="24" height="24"><path d="M511.5 958.9c-60.3 0-118.9-11.8-174-35.1-53.2-22.5-101-54.7-142.1-95.8-41-41-73.3-88.8-95.8-142.1-23.3-55.1-35.1-113.7-35.1-174s11.8-118.9 35.1-174c22.5-53.2 54.7-101 95.8-142.1 41-41 88.8-73.3 142.1-95.8 55.1-23.3 113.7-35.1 174-35.1s118.9 11.8 174 35.1c53.2 22.5 101 54.7 142.1 95.8 41 41 73.3 88.8 95.8 142.1 23.3 55.1 35.1 113.7 35.1 174s-11.8 118.9-35.1 174c-22.5 53.2-54.7 101-95.8 142.1-41 41-88.8 73.3-142.1 95.8-55.1 23.3-113.6 35.1-174 35.1z m0-838c-52.8 0-104 10.3-152.2 30.7-46.6 19.7-88.4 47.9-124.3 83.8s-64.1 77.7-83.8 124.3c-20.4 48.2-30.7 99.4-30.7 152.2s10.3 104 30.7 152.2c19.7 46.6 47.9 88.4 83.8 124.3s77.7 64.1 124.3 83.8c48.2 20.4 99.4 30.7 152.2 30.7s104-10.3 152.2-30.7c46.6-19.7 88.4-47.9 124.3-83.8 35.9-35.9 64.1-77.7 83.8-124.3 20.4-48.2 30.7-99.4 30.7-152.2s-10.3-104-30.7-152.2c-19.7-46.6-47.9-88.4-83.8-124.3-35.9-35.9-77.7-64.1-124.3-83.8-48.2-20.3-99.4-30.7-152.2-30.7z" fill="" p-id="2571"></path><path d="M652.2 423.3m-54.7 0a54.7 54.7 0 1 0 109.4 0 54.7 54.7 0 1 0-109.4 0Z" fill="" p-id="2572"></path><path d="M370.5 423.3m-54.7 0a54.7 54.7 0 1 0 109.4 0 54.7 54.7 0 1 0-109.4 0Z" fill="" p-id="2573"></path><path d="M511.5 775.9c-42.6 0-84.9-10.4-122.4-30.1-27-14.1-51.5-33.1-72.1-55.5-11.4-12.4-9.5-32 4.2-41.8 11.5-8.2 27.3-6.7 36.9 3.7 16.3 17.8 35.7 32.8 57.1 44 29.9 15.7 62.4 23.6 96.4 23.6 34 0 66.5-8 96.4-23.6 21.4-11.2 40.8-26.2 57.1-44 9.5-10.4 25.4-11.9 36.9-3.7 13.7 9.8 15.6 29.4 4.2 41.8-20.6 22.5-45.1 41.4-72.1 55.5-37.7 19.7-80 30.1-122.6 30.1z" fill="" p-id="2574"></path></svg>
          </span>
          <span v-else @click="handleToggleDialogEmoji">
            <svg t="1656983785218" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="1899" width="24" height="24"><path d="M513 959c-60.4 0-118.9-11.8-174.1-35.2-53.3-22.5-101.1-54.8-142.1-95.8-41.1-41.1-73.3-88.9-95.8-142.1-23.3-55.2-35.2-113.7-35.2-174.1s11.8-118.9 35.2-174.1c22.5-53.3 54.8-101.1 95.8-142.1 41.1-41.1 88.9-73.3 142.1-95.8C394.1 76.4 452.6 64.6 513 64.6s118.9 11.8 174.1 35.2c53.3 22.5 101.1 54.8 142.1 95.8 41.1 41.1 73.3 88.9 95.8 142.1 23.3 55.2 35.2 113.7 35.2 174.1s-11.8 118.9-35.2 174.1C902.5 739.1 870.3 787 829.2 828c-41.1 41.1-88.9 73.3-142.1 95.8C631.9 947.2 573.4 959 513 959zM513 120.6c-52.8 0-104.1 10.3-152.3 30.7-46.6 19.7-88.4 47.9-124.4 83.9-35.9 35.9-64.2 77.8-83.9 124.4-20.4 48.2-30.7 99.4-30.7 152.3s10.3 104.1 30.7 152.3c19.7 46.6 47.9 88.4 83.9 124.4 35.9 35.9 77.8 64.2 124.4 83.9C408.9 892.7 460.2 903 513 903s104.1-10.3 152.3-30.7c46.6-19.7 88.4-47.9 124.4-83.9 35.9-35.9 64.2-77.8 83.9-124.4 20.4-48.2 30.7-99.4 30.7-152.3s-10.3-104.1-30.7-152.3c-19.7-46.6-47.9-88.4-83.9-124.4-35.9-35.9-77.8-64.2-124.4-83.9C617.1 130.9 565.8 120.6 513 120.6z" fill="" p-id="1900"></path><path d="M630.6 398.3m-46.7 0a46.7 46.7 0 1 0 93.4 0 46.7 46.7 0 1 0-93.4 0Z" fill="" p-id="1901"></path><path d="M393.7 398.3m-46.7 0a46.7 46.7 0 1 0 93.4 0 46.7 46.7 0 1 0-93.4 0Z" fill="" p-id="1902"></path><path d="M513 790c-55.2 0-110-18.4-154.3-51.8-42.4-31.9-72.5-74.9-84.9-120.9-4.7-17.4-1-35.6 10-50 11.1-14.5 27.9-22.8 46.1-22.8l366.3-0.1h0c0 0 0 0 0 0 18.2 0 35 8.3 46.1 22.7 11 14.4 14.7 32.6 10 50-12.3 46-42.5 89-84.9 120.9C623 771.6 568.2 790 513 790zM696.1 600.4l-366.3 0.1c-0.3 0-1 0-1.7 0.8-0.5 0.7-0.4 1.2-0.3 1.4 9 33.5 32.5 66.5 64.5 90.6C427.1 719.6 469.9 734 513 734c43.1 0 85.9-14.4 120.7-40.6 32-24.1 55.5-57.2 64.5-90.7 0.1-0.3 0.2-0.7-0.3-1.4C697.2 600.4 696.4 600.4 696.1 600.4L696.1 600.4z" fill="" p-id="1903"></path></svg>
          </span>
        </p>
        <ul class="comment-buttons">
          <li class="middle" id="reply-title" v-if="isReply">
            <a
              href="javascript:;"
              class="button-cancel-reply"
              @click="cancelReply"
              >取消回复</a
            >
          </li>
          <li v-if="comment.content" class="middle">
            <a
              class="button-preview-edit"
              href="javascript:;"
              rel="nofollow noopener"
              @click="handlePreviewContent"
              >{{ previewMode ? "编辑" : "预览" }}</a
            >
          </li>
          <!-- <li
              class="middle"
              style="margin-right:5px"
            >
              <a
                class="button-preview-edit"
                href="javascript:void(0)"
                rel="nofollow noopener"
                @click="handleGithubLogin"
              >Github 登陆</a>
          </li>-->
          <li class="middle">
            <a
              class="button-submit"
              href="javascript:;"
              tabindex="5"
              rel="nofollow noopener"
              @click="handleSubmitClick"
              >提交</a
            >
          </li>
        </ul>
      </div>
      <transition name="emoji-fade">
          <VEmojiPicker
            :pack="emojiPack"
            @select="handleSelectEmoji"
            v-if="emojiDialogVisible"
          />
        </transition>
    </form>
  </section>
</template>

<script>
/* eslint-disable no-unused-vars */
import Vue from "vue";
import marked from "j-marked/lib/marked";
import md5 from "md5";
import VEmojiPicker from "./EmojiPicker/VEmojiPicker";
import emojiData from "./EmojiPicker/data/emojis.js";
import { renderedEmojiHtml } from "@/utils/emojiutil";
import {
  isEmpty,
  isObject,
  return2Br,
  getUrlKey,
  isQQ,
  queryStringify,
  isInVisibleArea,
} from "@/utils/util";
import commentApi from "../api/comment";
import axios from "axios";
import PopupInput from "./PopupInput";
import globals from "@/utils/globals.js";
import POWERMODE from "@/libs/activate-power-mode.js";

export default {
  name: "CommentEditor",
  components: {
    VEmojiPicker,
    PopupInput,
  },
  props: {
    targetId: {
      type: Number,
      required: false,
      default: 0,
    },
    target: {
      type: String,
      required: false,
      default: "posts",
      validator: function(value) {
        return ["posts", "sheets", "journals"].indexOf(value) !== -1;
      },
    },
    replyComment: {
      type: Object,
      required: false,
      default: () => {},
    },
    options: {
      required: false,
      default: [],
    },
    configs: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      emojiPack: emojiData,
      emojiDialogVisible: false,
      comment: {
        author: "",
        // avatar: "",
        authorUrl: "",
        email: "",
        content: "",
      },
      previewMode: false,
      infoes: [],
      warnings: [],
      successes: [],
      checkType: {
        back: "gravatar-check",
        icon: "fa fa-google",
      },
      globalData: globals,
      lockPullAvatar: false,
      avatar: "",
    };
  },
  computed: {
    renderedContent() {
      const html = this.comment.content ? marked(this.comment.content) : "";
      return return2Br(renderedEmojiHtml(html));
    },
    // commentValid() {
    //   return (
    //     !isEmpty(this.comment.author) &&
    //     !isEmpty(this.comment.email) &&
    //     !isEmpty(this.comment.content)
    //   );
    // },
    isReply() {
      return this.globalData.replyId != 0;
    },
    isCurrReply() {
      const isCurr =
        !this.replyComment || this.globalData.replyId == this.replyComment.id;

      // TODO：滚动定位有bug，先关闭，后面有时间来修复
      // if (isCurr) {
      //   // 获取当前评论组件相对于document的位置并跳转
      //   if (this.isReply) {
      //     this.viewJump((dom) => {
      //       // 获取定位并移动视角
      //       var rootOffsetTop = this.$root.$el.offsetTop;
      //       var offsetTop = dom.offsetTop + rootOffsetTop;
      //       var clientHeight = window.innerHeight;
      //       var objHeight = dom.offsetHeight;
      //       window.scrollTo(
      //         document.body.scrollWidth,
      //         offsetTop - clientHeight + objHeight + 20
      //       );
      //     });
      //   }
      // }
      return isCurr;
    },
    respondId() {
      return "respond-" + (!this.replyComment ? 0 : this.replyComment.id);
    },
  },
  watch: {
    options(n, o) {
      if (n && n.gravatar_source !== o.gravatar_source) {
        this.updateAvatar();
      }
    },
  },
  created() {
    // Get info from local storage
    var author = localStorage.getItem("comment-author");
    var authorUrl = localStorage.getItem("comment-authorUrl");
    var email = localStorage.getItem("comment-email");
    this.comment.author = author ? author : "";
    this.comment.authorUrl = authorUrl || "";
    // this.comment.avatar = this.avatar;
    this.comment.email = email ? email : "";
    this.updateAvatar();

    this.$nextTick(() => {
      POWERMODE.colorful = true; // make power mode colorful
      POWERMODE.shake = false; // turn off shake
      document.body.addEventListener("input", POWERMODE);
    });
  },
  methods: {
    updateAvatar() {
      var avatar = localStorage.getItem("avatar");
      this.avatar = avatar ? avatar : this.pullGravatarInfo(true);
    },
    handleSubmitClick() {
      if (isEmpty(this.comment.author)) {
        this.$tips("昵称不能为空！", 5000, this);
        return;
      }
      if (isEmpty(this.comment.email)) {
        this.$tips("邮箱不能为空！", 5000, this);
        return;
      }
      if (isEmpty(this.comment.content)) {
        this.$tips("评论内容不能为空！", 5000, this);
        return;
      }
      // Submit the comment
      // this.comment.authorUrl = this.avatar; //后台目前没提供头像字段，暂时用authorUrl来存
      // this.comment.avatar = this.avatar;
      this.comment.postId = this.targetId;
      if (this.replyComment) {
        // Set parent id if available
        this.comment.parentId = this.replyComment.id;
      }
      commentApi
        .createComment(this.target, this.comment)
        .then((response) => {
          // Store comment author, email, authorUrl
          localStorage.setItem("comment-author", this.comment.author);
          localStorage.setItem("comment-email", this.comment.email);
          localStorage.setItem("comment-authorUrl", this.comment.authorUrl);
          localStorage.setItem("avatar", this.avatar);

          // clear comment
          this.comment.content = "";
          this.previewMode = false;
          this.handleCommentCreated(response.data.data);
        })
        .catch((error) => {
          this.previewMode = false;
          this.handleFailedToCreateComment(error.response);
        });
    },
    handlePreviewContent() {
      this.previewMode = !this.previewMode;
    },
    handleCommentCreated(createdComment) {
      if (createdComment.status === "PUBLISHED") {
        // 成功后直接新增新的评论node
        try {
          this.createdNewNode(createdComment);
          this.$tips("评论成功！", 3500, this, "success");
          this.$parent.$emit("post-success", {
            target: this.target,
            targetId: this.targetId,
          });
        } catch {
          this.$tips("评论成功，刷新即可显示最新评论！", 5000, this, "success");
        }
      } else {
        this.$tips(
          "您的评论已经投递至博主，等待博主审核！",
          5000,
          this,
          "success"
        );
      }
    },
    handleAvatarError(e) {
      const img = e.target || e.srcElement;
      img.src = this.configs.avatarError;
      img.onerror = null;
    },
    createdNewNode(newComment) {
      let elDom = this.$root.$el;
      let pr = {
        targetId: this.targetId,
        target: this.target,
        options: this.options,
        configs: this.configs,
        comment: newComment,
      };

      pr =
        newComment.parentId == 0
          ? pr
          : {
              ...pr,
              ...{
                isChild: true,
                parent: this.replyComment,
                depth: this.$parent.selfAddDepth,
              },
            };

      const CommentNode = () => import("./CommentNode.vue");
      // 创建一个组件
      let comment = new Vue({
        render: (h) => {
          return h(CommentNode, {
            props: pr,
          });
        },
      });
      let dom;
      if (newComment.parentId == 0) {
        if (elDom.getElementsByClassName("commentwrap").length > 0) {
          dom = elDom.getElementsByClassName("commentwrap")[0];
        } else {
          dom = document.createElement("ul");
          dom.setAttribute("class", "commentwrap");
          let emptyDom = elDom.getElementsByClassName("comment-empty")[0];
          emptyDom.parentNode.replaceChild(dom, emptyDom);
        }
      } else {
        // 获取li
        let parentDom = elDom.getElementsByClassName(
          "comment-" + this.replyComment.id
        )[0];
        // 获取li下的ul节点
        let replyDom = parentDom.getElementsByTagName("ul");
        if (replyDom.length > 0) {
          dom = replyDom[0];
        } else {
          dom = document.createElement("ul");
          dom.setAttribute("class", "children");
          parentDom.appendChild(dom);
        }
      }
      let nodeDom = document.createElement("div");
      if (dom.children[0]) {
        dom.insertBefore(nodeDom, dom.children[0]);
      } else {
        dom.appendChild(nodeDom);
      }

      comment.$mount(nodeDom);
    },
    handleFailedToCreateComment(response) {
      if (response.status === 400) {
        this.$tips(response.data.message, 3500, this, "danger");
        if (response.data) {
          const errorDetail = response.data.data;
          if (isObject(errorDetail)) {
            Object.keys(errorDetail).forEach((key) => {
              this.$tips(errorDetail[key], 3500, this, "danger");
            });
          }
        }
      }
    },
    handleToggleDialogEmoji() {
      this.emojiDialogVisible = !this.emojiDialogVisible;
    },
    handleSelectEmoji(args) {
      let emoji, type;
      let emojiComment;

      if (args.length == 0) return;

      if (args.length > 0) {
        emoji = args[0];
      }
      if (args.length > 1) {
        type = args[1];
      }
      if (!type) {
        emojiComment = emoji.name;
      } else {
        if (type === "Math") {
          emojiComment = "f(x)=∫(" + emoji.name + ")sec²xdx";
        } else if (type === "BBCode") {
          // 区分扩展名，gif末尾加个感叹号
          emojiComment = `:${emoji.name +
            (emoji.extension === "gif" ? "!" : "")}:`;
        }
      }
      this.comment.content += " " + emojiComment + " ";
    },
    handleGithubLogin() {
      const githubOauthUrl = "http://github.com/login/oauth/authorize";
      const query = {
        client_id: "a1aacd842bc158abd65b",
        redirect_uri: window.location.href,
        scope: "public_repo",
      };
      window.location.href = `${githubOauthUrl}?${queryStringify(query)}`;
    },
    handleGetGithubUser() {
      const accessToken = this.handleGetGithubAccessToken();
      axios
        .get(
          "https://cors-anywhere.herokuapp.com/https://api.github.com/user",
          {
            params: {
              access_token: accessToken,
            },
          }
        )
        .then(function(response) {
          this.$tips(response);
        })
        .catch((error) => {
          this.$tips(error);
        });
    },
    handleGetGithubAccessToken() {
      const code = getUrlKey("code");
      if (code) {
        axios
          .get(
            "https://cors-anywhere.herokuapp.com/https://github.com/login/oauth/access_token",
            {
              params: {
                client_id: "a1aacd842bc158abd65b",
                client_secret: "0daedb3923a4cdeb72620df511bdb11685dfe282",
                code: code,
              },
            }
          )
          .then(function(response) {
            let args = response.split("&");
            let arg = args[0].split("=");
            let access_token = arg[1];
            this.$tips(access_token);
            return access_token;
          })
          .catch((error) => {
            this.$tips(error);
          });
      }
    },
    cancelReply(e) {
      e.stopPropagation();
      // 当replyId为0时则为回复博主
      this.globalData.replyId = 0;
      this.globalData.isReplyData = false;

      // TODO：滚动定位有bug，先关闭，后面有时间来修复
      // 取消回复后，将跳转至回复前的地方
      // var targetDom = this.$el.previousSibling;
      // var offsetTop = targetDom.offsetTop + this.$root.$el.offsetTop;
      // window.scrollTo(document.body.scrollWidth, offsetTop);
    },
    pullInfo() {
      let author = this.comment.author;

      // 暂时注释拉取QQ头像功能
      // if (author.length != 0 && isQQ(author)) {
      //   // 如果是QQ号，则拉取QQ头像
      //   this.pullQQInfo(() => {
      //     this.$tips("拉取QQ信息失败！尝试拉取Gravatar", 2000, this);
      //     // 如果QQ拉取失败，则尝试拉取Gravatar
      //     this.pullGravatarInfo();
      //   });
      //   return;
      // }
      // // 防止刚拉取完QQ头像就拉取Gravatar头像
      // if (this.lockPullAvatar) {
      //   this.lockPullAvatar = false;
      //   return;
      // }

      // 拉取Gravatar头像
      this.pullGravatarInfo();
    },
    pullQQInfo(errorQQCallback) {
      let _self = this;
      // 拉取QQ昵称，头像等
      axios
        .get("https://api.lixingyong.com/api/qq", {
          params: {
            id: _self.comment.author,
          },
        })
        .then(function(res) {
          let data = res.data;
          if (!!data.code && data.code == 500) {
            errorQQCallback();
          }
          _self.$tips("拉取QQ头像成功！", 2000, _self, "success");
          _self.comment.author = data.nickname;
          _self.comment.email = data.email;
          _self.avatar = data.avatar;
          _self.lockPullAvatar = true;
        })
        .catch(() => {
          errorQQCallback();
        });
    },
    pullGravatarInfo(isDefault) {
      const gravatarMd5 = `/${md5(this.comment.email)}`;
      // !!优先从主题配置取，取不到才从后台配置取
      const gravatarSource =
        this.configs.gravatarSource ||
        this.options.gravatar_source ||
        this.configs.gravatarSourceDefault;
      const avatar =
        gravatarSource +
        `${gravatarMd5}?s=256&d=` +
        this.options.comment_gravatar_default || 'mm';
      if (!isDefault) {
        this.avatar = avatar;
      } else {
        return avatar;
      }
    },
    // 锚点跳转
    viewJump(callback, targetDom) {
      // dom为异步更新，因此务必放在nextTick内，否则无法获取到dom。
      this.$nextTick(() => {
        var dom = targetDom || this.$el;
        // 若当前dom不在可视范围内，则将视角移动至dom下
        if (!isInVisibleArea(dom, this.$root.$el, "bottom")) {
          callback(dom);
        }
      });
    },
  },
};
</script>
