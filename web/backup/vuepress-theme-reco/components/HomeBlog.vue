<template>
  <div class="home-blog">
    <div class="hero" :style="{ ...bgImageStyle }">
      <div>
        <ModuleTransition>
          <img
            class="hero-img"
            v-if="recoShowModule && $frontmatter.heroImage"
            :style="heroImageStyle || {}"
            :src="$withBase($frontmatter.heroImage)"
            alt="hero"
          />
        </ModuleTransition>

        <ModuleTransition delay="0.04">
          <h1 v-if="recoShowModule && $frontmatter.heroText !== null">
            {{ $frontmatter.heroText || $title || 'vuePress-theme-reco' }}
          </h1>
        </ModuleTransition>

        <ModuleTransition delay="0.08">
          <p v-if="recoShowModule && $frontmatter.tagline !== null" class="description">
            <!-- Esto se comenta -->
            <!-- {{ $frontmatter.tagline || $description || 'Welcome to your vuePress-theme-reco site' }} -->
            {{ $themeConfig.jobs }}
          </p>
        </ModuleTransition>
          <!-- More -->
        <ModuleTransition delay="0.10">
          <a href="#homeBlog">
            <div class="chevron-container">
              <div class="chevron"></div>
              <div class="chevron"></div>
              <div class="chevron"></div>
            </div>
        </a>
         </ModuleTransition>
      </div>
    </div>
    <!-- More -->
    <div id="homeBlog"></div>
    
    <ModuleTransition delay="0.16">
      <div v-show="recoShowModule" class="home-blog-wrapper">
        <div class="blog-list">
          <!-- 博客列表 -->
          <note-abstract
            :data="$recoPosts"
            :currentPage="currentPage"></note-abstract>
          <!-- 分页 -->
          <pagation
            class="pagation"
            :total="$recoPosts.length"
            :currentPage="currentPage"
            @getCurrentPage="getCurrentPage" />
        </div>
        <div class="info-wrapper">
          <PersonalInfo/>
          <h4><reco-icon icon="reco-category" /> {{homeBlogCfg.category}}</h4>
          <ul class="category-wrapper">
            <li class="category-item" v-for="(item, index) in this.$categories.list" :key="index">
              <router-link :to="item.path">
                <span class="category-name">{{ item.name }}</span>
                <span class="post-num" :style="{ 'backgroundColor': getOneColor() }">{{ item.pages.length }}</span>
              </router-link>
            </li>
          </ul>
          <hr>
          <h4 v-if="$tags.list.length !== 0"><reco-icon icon="reco-tag" /> {{homeBlogCfg.tag}}</h4>
          <TagList @getCurrentTag="getPagesByTags" />
          <h4 v-if="$themeConfig.friendLink && $themeConfig.friendLink.length !== 0"><reco-icon icon="reco-friend" /> {{homeBlogCfg.friendLink}}</h4>
          <FriendLink />
        </div>
      </div>
    </ModuleTransition>

    <ModuleTransition delay="0.24">
      <Content v-show="recoShowModule" class="home-center" custom/>
    </ModuleTransition>
  </div>
</template>

<script>
import TagList from '@theme/components/TagList'
import FriendLink from '@theme/components/FriendLink'
import NoteAbstract from '@theme/components/NoteAbstract'
import pagination from '@theme/mixins/pagination'
import { ModuleTransition, RecoIcon } from '@vuepress-reco/core/lib/components'
import PersonalInfo from '@theme/components/PersonalInfo'
import { getOneColor } from '@theme/helpers/other'

export default {
  mixins: [pagination],
  components: { NoteAbstract, TagList, FriendLink, ModuleTransition, PersonalInfo, RecoIcon },
  data () {
    return {
      recoShow: false,
      currentPage: 1,
      tags: []
    }
  },
  computed: {
    recoShowModule () {
      return this.$parent.recoShowModule
    },
    homeBlogCfg () {
      return this.$recoLocales.homeBlog
    },
    actionLink () {
      const {
        actionLink: link,
        actionText: text
      } = this.$frontmatter

      return {
        link,
        text
      }
    },
    heroImageStyle () {
      return this.$frontmatter.heroImageStyle || {}
    },
    bgImageStyle () {
      const initBgImageStyle = {
        textAlign: 'center',
        overflow: 'hidden',
        background: `
          url(${this.$frontmatter.bgImage
    ? this.$withBase(this.$frontmatter.bgImage)
    : require('../images/bg-jl-01.svg')}) center/cover no-repeat
        `
      }
      const {
        bgImageStyle
      } = this.$frontmatter

      return bgImageStyle ? { ...initBgImageStyle, ...bgImageStyle } : initBgImageStyle
    },
    heroHeight () {
      return document.querySelector('.hero').clientHeight
    }
  },
  mounted () {
    this.recoShow = true
    this._setPage(this._getStoragePage())
  },
  methods: {
    // 获取当前页码
    getCurrentPage (page) {
      this._setPage(page)
      setTimeout(() => {
        window.scrollTo(0, this.heroHeight)
      }, 100)
    },
    // 根据分类获取页面数据
    getPages () {
      let pages = this.$site.pages
      pages = pages.filter(item => {
        const { home, date } = item.frontmatter
        return !(home == true || date === undefined)
      })
      // reverse()是为了按时间最近排序排序
      this.pages = pages.length == 0 ? [] : pages
    },
    getPagesByTags (tagInfo) {
      this.$router.push({ path: tagInfo.path })
    },
    _setPage (page) {
      this.currentPage = page
      this.$page.currentPage = page
      this._setStoragePage(page)
    },
    getOneColor
  }
}
</script>

<style lang="stylus">

/* cevron */
.chevron-container {
 display: flex;
 justify-content: center;
 align-items: center;
 width: 100%;
 padding-top: 0.5rem;
}

.chevron {
  position: absolute;
  width:  0.5rem * 3.5;
  height:  0.5rem * 0.8;
  opacity: 0;
  transform: scale(0.3);
  animation: move-chevron 3s ease-out infinite;
}

.chevron:first-child {
  animation: move-chevron 3s ease-out 1s infinite;
}

.chevron:nth-child(2) {
  animation: move-chevron 3s ease-out 2s infinite;
}

.chevron:before,
.chevron:after {
 content: '';
 position: absolute;
 top: 0;
 height: 100%;
 width: 50%;
 background: #4586ad;
}

.chevron:before {
 left: 0;
 transform: skewY(30deg);
}

.chevron:after {
 right: 0;
 width: 50%;
 transform: skewY(-30deg);
}

@keyframes move-chevron {
 25% {
  opacity: 1;
	}
 33.3% {
  opacity: 1;
  transform: translateY(0.5rem * 3.8);
 }
 66.6% {
  opacity: 1;
  transform: translateY(0.5rem * 5.2);
 }
 100% {
  opacity: 0;
  transform: translateY(0.5rem * 8) scale(0.5);
 }
}


.home-blog {
  padding: 0;
  margin: 0px auto;

  #homeBlog {
    left 0
    bottom 0px
    width 100%
    position absolute
  }
  .hero {
    margin $navbarHeight auto 0
    position relative
    box-sizing border-box
    padding 0 20px
    height 100vh
    display flex
    align-items center
    justify-content center
    .hero-img {
      max-width: 300px;
      margin: 0 auto 1.5rem
    }

    h1 {
      display: block;
      margin:0 auto 1.8rem;
      font-size: 2.5rem;
    }

    .description {
      margin: 1.8rem auto;
      font-size: 1.6rem;
      line-height: 1.3;
      text-align: center;
    }
  }
  .home-blog-wrapper {
    display flex
    align-items: flex-start;
    margin 20px auto 0
    padding 0 20px
    max-width $homePageWidth
    .blog-list {
      flex auto
      width 0
      .abstract-wrapper {
        .abstract-item:last-child {
          margin-bottom: 0px;
        }
      }
    }
    .info-wrapper {
      position -webkit-sticky;
      position sticky;
      top 70px
      overflow hidden
      transition all .3s
      margin-left 15px
      flex 0 0 300px
      height auto
      box-shadow var(--box-shadow)
      border-radius $borderRadius
      box-sizing border-box
      padding 0 15px
      background var(--background-color)
      transition background 1s
      &:hover {
        box-shadow var(--box-shadow-hover)
      }
      h4 {
        color var(--text-color)
      }
      .category-wrapper {
        list-style none
        padding-left 0
        .category-item {
          margin-bottom .4rem
          padding: .4rem .8rem;
          transition: all .5s
          border-radius $borderRadius
          box-shadow var(--box-shadow)
          background-color var(--background-color)
          transition background-color 1s
          &:hover {
            transform scale(1.04)
            a {
              color $accentColor
            }
          }
          a {
            display flex
            justify-content: space-between
            align-items: center
            color var(--text-color)
            .post-num {
              width 1.6rem;
              height 1.6rem
              text-align center
              line-height 1.6rem
              border-radius $borderRadius
              background #eee
              font-size 13px
              color #fff
            }
          }
        }
      }
    }
  }
}



@media (max-width: $MQMobile) {
  .chevron-container {
    display: none
  }
  .home-blog {
    .hero {
      height 450px
      img {
        max-height: 300px;
        max-width: 100%;
        margin: 2rem auto 1.2rem;
      }

      h1 {
        margin: 0 auto 1.8rem ;
        font-size: 2rem;
      }

      .description {
        font-size: 1.2rem;
      }

      .action-button {
        font-size: 1rem;
        padding: 0.6rem 1.2rem;
      }
    }
    .home-blog-wrapper {
      display block!important
      .blog-list {
        width auto
      }
      .info-wrapper {
        // display none!important
        margin-left 0
        .personal-info-wrapper {
          display none
        }
      }
    }
  }
}

@media (max-width: $MQMobileNarrow) {
  .chevron-container {
    display: none
  }
  .home-blog {
    .hero {
      height 450px
      img {
        max-height: 210px;
        max-width: 100%;
        margin: 2rem auto 1.2rem;
      }

      h1 {
        margin: 0 auto 1.8rem ;
        font-size: 2rem;
      }

      h1, .description, .action {
        // margin: 1.2rem auto;
      }

      .description {
        font-size: 1.2rem;
      }

      .action-button {
        font-size: 1rem;
        padding: 0.6rem 1.2rem;
      }
    }

    .home-blog-wrapper {
      display block!important
      .blog-list {
        width auto
      }
      .info-wrapper {
        // display none!important
        margin-left 0
        .personal-info-wrapper {
          display none
        }
      }
    }
  }
}




</style>
