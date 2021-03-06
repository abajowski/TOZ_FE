<template>
  <div class="container">
    <div class="loader" v-if="loading"></div>
    <div class="errors" v-if="errors.length">
      <h2 v-for="error of errors">{{ error.message }}</h2>
    </div>
    <div class="row" v-if="!errors.length && !loading">
      <div class=" col-12 col-lg-9">
        <div class="row">
          <div class="col hidden-sm-up">
            <p class="text-left top-date">{{ $t('news.added') }} {{ convertTimeStamp(newsDetails.published) }}</p>
          </div>
        </div>
        <div class="row">
          <div class="col mt-3 mb-3">
            <h3 class="title"><u>{{ newsDetails.title }}</u></h3>
          </div>
        </div>
        <div class="row">
          <div class="col-12">
            <div class="row">
              <div class="col">
                <img :src="setUrl(newsDetails)" @error="defaultImg()" class="img-fluid float-left main-img">
              </div>
            </div>
            <div class="row">
              <div class="col mt-4 mb-4">
                <p class="text-left news-info">{{ $t('news.added') }} {{ convertTimeStamp(newsDetails.published) }}</p>
                <p class="text-left">{{ newsDetails.contents }}</p>
              </div>
            </div>
            <div class="row">
              <div class="col">
                <hr class="divider">
                <p class="next-news-section-title">{{ $t('news.nextNews') }}</p>
              </div>
            </div>
            <div class="row">
              <div class="col">
                <carousel class="carousel"
                :navigationEnabled="settings.navigationEnabled"
                :paginationEnabled="settings.paginationEnabled"
                :navigationClickTargetSize="settings.navigationClickTargetSize"
                :perPageCustom="settings.perPageCustom">
                  <slide v-for="item of nextNews" :key="item.id"
                    v-show="item.id !== newsDetails.id && isNewsExpired(item.published)">
                    <div class="card" @click="openNews(item.id)">
                      <img class="img-fluid card-img-top" :src="setUrl(item)" @error="defaultImg()" alt="">
                      <div class="card-block">
                        <h4 class="card-title">
                        </h4>
                        <p class="card-text">{{ item.title }}</p>
                        <p class="card-text"><small class="text-muted">{{ convertTimeStamp(item.published) }}</small></p>
                      </div>
                    </div>
                  </slide>
                </carousel>
              </div>
            </div>
          </div>
        </div>
      </div>
      <Sidebar></Sidebar>
    </div>
  </div>
</template>

<script>
  import moment from 'moment'
  import { Carousel, Slide } from 'vue-carousel'
  import Sidebar from './Sidebar.vue'

  export default {
    name: 'NewsDetails',
    components: {
      Carousel,
      Slide,
      Sidebar
    },
    data () {
      return {
        settings: {
          navigationEnabled: true,
          paginationEnabled: false,
          navigationClickTargetSize: 0,
          perPageCustom: [[300, 1], [500, 2], [1864, 3]]
        },
        errors: [],
        newsDetails: {},
        nextNews: {},
        id: this.$route.params.id,
        loading: true
      }
    },
    created () {
      this.fetchData()
      this.fetchNextNews()
    },
    watch: {
      '$route': 'reloadPage'
    },
    methods: {
      reloadPage () {
        location.reload()
      },
      convertTimeStamp (published) {
        const date = moment(published).locale(this.$t('common.code'))
        return date.format(this.$t('common.dateFormat'))
      },
      addMonths (date, months) {
        date.setMonth(date.getMonth() + months)
        return date
      },
      isNewsExpired (itemPublished) {
        if (itemPublished > this.addMonths(new Date(), -6)) {
          return true
        } else {
          return false
        }
      },
      fetchData () {
        this.$http.get(this.apiUrl + 'news/' + this.id)
          .then(response => {
            this.newsDetails = response.data
            this.loading = false
          })
          .catch(error => {
            this.errors.push(error)
            this.loading = false
          })
      },
      fetchNextNews () {
        this.$http.get(this.apiUrl + 'news?type=RELEASED&isOrdered=true')
          .then(response => {
            this.nextNews = response.data
            this.loading = false
          })
          .catch(error => {
            this.errors.push(error)
            this.loading = false
          })
      },
      setUrl (news) {
        if (news.imageUrl === '' || news.imageUrl === null || news.imageUrl === undefined) {
          return this.defaultImg()
        } else if (news.imageUrl.includes('data')) {
          return news.imageUrl
        } else {
          return this.apiUrl.substr(0, this.apiUrl.length - 4) + news.imageUrl
        }
      },
      defaultImg () {
        const imgUrl = require('../assets/default_avatar_dog.svg')
        return imgUrl
      },
      openNews (itemId) {
        this.$router.push({ name: 'news', params: { id: itemId }, query: { type: 'RELEASED', isShortened: false, isOrdered: true } })
      }
    }
  }
</script>

<style lang="sass" scoped>
  @import "../assets/styles/loader.css"
  @import "../assets/styles/variables.sass"

  p
    line-height: 1.7

  .card
    cursor: pointer
    margin: 5px
    min-height: 500px
    &:hover
      background-color: inherit
      border: 1px solid $green

  .card-text
    font-size: 1.41rem

  .divider
    background-color: $green
    height: 5px

  .img-fluid
    min-width: 100%

  .main-img
    height: 100%
    width: 100%

  .news-info
    font-size: 1rem
    @media (max-width: 544px)
      display: none

  .next-news-section-title
    font-size: 2rem
    font-weight: bold

  .text-left
    line-height: 1.35

  .text-muted
    font-size: .9rem

  .title
    text-align: left

  .top-date
    font-size: 1.4rem
    margin-top: 35px
    margin-bottom: 12px

  .wrapper
    @media (min-width: 544px)
      margin-left: 50px
</style>
