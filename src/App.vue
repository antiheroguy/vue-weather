<template>
  <div id="app">
    <div
      class="main"
      v-if="now"
      @click.once="play()"
    >
      <div class="main-wthree-row">
        <div class="agileits-top">
          <div class="agileinfo-top-row">
            <div class="agileinfo-top-img">
              <span></span>
            </div>
            <h3>{{ now.temp }}<sup class="degree">°</sup><span>C</span></h3>
            <p>{{ city }}</p>
            <div class="agileinfo-top-time">
              <div class="date-time">
                <div class="dmy">
                  <div id="txt">{{ time }}</div>
                  <div class="date">{{ date.toUpperCase() }}</div>
                </div>
                <div class="clear"></div>
              </div>
            </div>
          </div>
        </div>
        <div class="w3ls-bottom">
          <h2>Sắp tới</h2>
          <div
            class="demo"
            v-for="item in next"
            :key="item.dt"
          ></div>
          <carousel
            :items="6"
            :autoplay="true"
            :loop="false"
            :nav="false"
            :dots="false"
          >
            <div
              class="item"
              v-for="item in next"
              :key="item.dt"
            >
              <div class="biseller-column">
                <p>{{ item.dt | date('HH:mm') }}</p>
                <p class="today">{{ item.dt | date('MM/DD') }}</p>
                <a class="lightbox">
                  <img
                    :src="`${imageBaseUrl}/${item.weather[0].icon}@2x.png`"
                    :title="item.weather[0].description"
                  />
                </a>
                <p>{{ item.main.temp }}<sup class="degree">°</sup><span>C</span></p>
              </div>
            </div>
          </carousel>
        </div>
        <div class="w3ls-bottom2">
          <div class="ac-container">
            <input
              id="ac-1"
              name="accordion-1"
              type="checkbox"
            />
            <label
              for="ac-1"
              class="grid1"
            >Trong ngày</label>
            <article class="ac-small">
              <div class="wthree-grids">
                <div
                  class="wthree-grids-row"
                  v-for="item in today"
                  :key="item.dt"
                >
                  <ul class="top">
                    <li>{{ item.dt | date('HH:mm') }}</li>
                    <li class="wthree-img"><img
                        :src="`${imageBaseUrl}/${item.weather[0].icon}@2x.png`"
                        :title="item.weather[0].description"
                      /></li>
                    <li class="wthree-temp">{{ item.main.temp_min }} <sup class="degree">°</sup><span>C</span></li>
                    <li class="wthree-temp">{{ item.main.temp_max }} <sup class="degree">°</sup><span>C</span></li>
                  </ul>
                  <div class="clear"> </div>
                </div>
              </div>
            </article>
          </div>
        </div>
      </div>
    </div>
    <audio
      src="/background.mp3"
      autoplay
      loop
      ref="audio"
    ></audio>
  </div>
</template>
<script>
import carousel from 'vue-owl-carousel'
import moment from 'moment'
import axios from 'axios'

export default {
  name: 'app',

  data: () => ({
    now: null,
    city: null,
    today: [],
    next: [],
    date: '',
    time: '',
  }),

  computed: {
    imageBaseUrl() {
      return process.env ? process.env.VUE_APP_IMAGE_BASE_URL : ''
    }
  },

  created() {
    setInterval(() => {
      moment.locale('vi')
      this.time = moment().format('HH:mm:ss')
      this.date = moment().format('dddd, DD MMMM gggg')
    }, 1)

    this.fetch()
  },

  methods: {
    async fetch() {
      const { data: cityName } = await axios.get(process.env.VUE_APP_GEO_ENDPOINT)
      this.city = cityName

      const [{ data: { main } }, { data: { list } }] = await axios.all([
        axios.get(`${process.env.VUE_APP_WEATHER_ENDPOINT}/weather`, {
          params: {
            appid: process.env.VUE_APP_API_KEY,
            q: cityName,
            lang: 'vi',
            units: 'metric'
          }
        }),
        axios.get(`${process.env.VUE_APP_WEATHER_ENDPOINT}/forecast`, {
          params: {
            appid: process.env.VUE_APP_API_KEY,
            q: cityName,
            lang: 'vi',
            units: 'metric'
          }
        })
      ])

      this.now = main
      this.next = list
      this.today = list.filter(item => moment.unix(item.dt).format('DD') === moment().format('DD'))
    },

    play() {
      this.$refs.audio.play()
    },
  },

  filters: {
    date(value, format) {
      return moment.unix(value).format(format)
    },
  },

  components: {
    carousel
  }
}
</script>

<style lang="less">
html {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
body {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
  font-family: "Open Sans", sans-serif;
  background: url("./assets/main.jpg") no-repeat 0px 0px;
  background-attachment: fixed;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
  -ms-background-size: cover;
  background-size: cover;
}
div {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
span {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
applet {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
object {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
iframe {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
h1 {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
h2 {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
h3 {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
h4 {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
h5 {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
h6 {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
p {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
blockquote {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
  quotes: none;
  &:before {
    content: "";
    content: none;
  }
  &:after {
    content: "";
    content: none;
  }
}
pre {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
a {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
  text-decoration: none;
}
abbr {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
acronym {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
address {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
big {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
cite {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
code {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
del {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
dfn {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
em {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
img {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
  max-width: 100%;
}
ins {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
kbd {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
q {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
  quotes: none;
  &:before {
    content: "";
    content: none;
  }
  &:after {
    content: "";
    content: none;
  }
}
s {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
samp {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
small {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
strike {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
strong {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
sub {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
sup {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
  position: static;
}
tt {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
var {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
b {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
u {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
i {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
dl {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
dt {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
dd {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
ol {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
  list-style: none;
  margin: 0px;
  padding: 0px;
}
nav {
  ul {
    margin: 0;
    padding: 0;
    border: 0;
    font-size: 100%;
    font: inherit;
    vertical-align: baseline;
  }
  li {
    margin: 0;
    padding: 0;
    border: 0;
    font-size: 100%;
    font: inherit;
    vertical-align: baseline;
  }
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  display: block;
}
fieldset {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
form {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
label {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
legend {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
table {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
  border-collapse: collapse;
  border-spacing: 0;
}
caption {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
tbody {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
tfoot {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
thead {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
tr {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
th {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
td {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
article {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  display: block;
}
aside {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  display: block;
}
canvas {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
details {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  display: block;
}
embed {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
figure {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  display: block;
}
figcaption {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  display: block;
}
footer {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  display: block;
}
header {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  display: block;
}
hgroup {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  display: block;
}
menu {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  display: block;
}
output {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
ruby {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
section {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  display: block;
}
summary {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
time {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
mark {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
audio {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
video {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
ul {
  list-style: none;
  margin: 0px;
  padding: 0px;
}
.txt-rt {
  text-align: right;
}
.txt-lt {
  text-align: left;
}
.txt-center {
  text-align: center;
}
.float-rt {
  float: right;
}
.float-lt {
  float: left;
}
.clear {
  clear: both;
}
.pos-relative {
  position: relative;
}
.pos-absolute {
  position: absolute;
}
.vertical-base {
  vertical-align: baseline;
}
.vertical-top {
  vertical-align: top;
}
nav.vertical {
  ul {
    li {
      display: block;
    }
  }
}
nav.horizontal {
  ul {
    li {
      display: inline-block;
    }
  }
}
.main {
  h2 {
    font-size: 1.2em;
    color: #00bbb4;
    text-align: right;
    margin-right: 2em;
    font-family: "Lora", serif;
  }
  a.lightbox {
    display: block;
  }
}
.main-wthree-row {
  width: 32%;
  margin: 3.5em auto 2em;
  background: #fff;
}
.agileits-top {
  background: url("./assets/top.jpg") no-repeat 0px 0px;
  background-size: cover;
  min-height: 310px;
}
.agileinfo-top-row {
  position: relative;
  padding: 0 3em;
  h3 {
    font-size: 4em;
    color: #ffd61c;
    padding-top: 0.2em;
    text-align: right;
    span {
      font-size: 0.5em;
      color: #fff;
    }
  }
  p {
    margin: 0;
    color: #fff;
    font-size: 1.5em;
    text-align: right;
    font-weight: 600;
  }
}
.agileinfo-top-img {
  span {
    background: url("./assets/logo.png") no-repeat;
    background-size: cover;
    display: block;
    height: 139px;
    width: 150px;
    position: absolute;
    top: -15%;
    left: -1%;
  }
}
.agileinfo-top-time {
  padding: 2.5em 0 0;
}
#txt {
  color: #fff;
  font-size: 2em;
  font-weight: 200;
  letter-spacing: 3px;
  font-family: "Lora", serif;
}
.date {
  font-size: 0.9em;
  color: #ffd61c;
  margin-top: 0.5em;
  font-weight: 200;
}
.w3ls-bottom {
  background: url("./assets/bottom.png") no-repeat -1px 0px;
  background-size: 103%;
  padding: 3em 1em 0;
  margin-top: -4em;
}
.biseller-column {
  p {
    font-size: 0.9em;
    color: #999;
    font-weight: 400;
  }
  &:hover {
    p {
      color: #00bbb4;
      cursor: grab;
    }
  }
}
.owl-carousel.owl-theme {
  .item {
    padding: 15px 0px;
    margin: 1em 1em;
    color: #fff;
    -webkit-border-radius: 3px;
    -moz-border-radius: 3px;
    -o-border-radius: 3px;
    -ms-border-radius: 3px;
    border-radius: 3px;
    text-align: center;
    .today {
      font-weight: bold;
      font-size: 12px;
    }
  }
}
.customNavigation {
  text-align: center;
}
.w3ls-bottom2 {
  padding: 0 2em 2em;
}
.wthree-grids {
  ul {
    padding: 0.8em 0 0;
    display: flex;
    align-items: center;
    li {
      display: inline-block;
      width: 25%;
      line-height: 2em;
      font-size: 0.9em;
      color: #888;
    }
    li.wthree-img {
      text-align: center;
      img {
        width: 35px;
        height: 35px;
      }
    }
  }
  ul.top {
    border: none;
  }
}
.wthree-temp {
  text-align: right;
}
.ac-container {
  label {
    padding-right: 40px;
    position: relative;
    z-index: 20;
    display: inline-block;
    cursor: pointer;
    color: #ff8b00;
    line-height: 33px;
    font-size: 1em;
    font-weight: 600;
    &:hover {
      color: #00bbb4;
    }
    &:after {
      content: "";
      position: absolute;
      width: 15px;
      height: 11px;
      right: 13px;
      top: 15px;
      background: url("./assets/arrow_up.png") no-repeat -4px -5px;
      -webkit-transition: 0.5s all;
      -moz-transition: 0.5s all;
      -o-transition: 0.5s all;
      -ms-transition: 0.5s all;
      transition: 0.5s all;
    }
  }
  input {
    &:checked {
      & + label {
        color: #00bbb4;
        &:hover {
          color: #00bbb4;
          &:after {
            content: "";
            position: absolute;
            width: 15px;
            height: 11px;
            right: 13px;
            top: 15px;
            background: url("./assets/arrow_up.png") no-repeat -4px -5px;
            -webkit-transition: 0.5s all;
            -moz-transition: 0.5s all;
            -o-transition: 0.5s all;
            -ms-transition: 0.5s all;
            transition: 0.5s all;
            color: #000;
            -webkit-transform: rotate(180deg);
            -moz-transform: rotate(180deg);
            -o-transform: rotate(180deg);
            -ms-transform: rotate(180deg);
            transform: rotate(180deg);
            top: 10px;
          }
        }
        &:after {
          color: #000;
          -webkit-transform: rotate(180deg);
          -moz-transform: rotate(180deg);
          -o-transform: rotate(180deg);
          -ms-transform: rotate(180deg);
          transform: rotate(180deg);
          top: 10px;
        }
      }

      & ~ article {
        -webkit-transition: height 0.5s ease-in-out, box-shadow 0.1s linear;
        -moz-transition: height 0.5s ease-in-out, box-shadow 0.1s linear;
        -o-transition: height 0.5s ease-in-out, box-shadow 0.1s linear;
        -ms-transition: height 0.5s ease-in-out, box-shadow 0.1s linear;
        transition: height 0.5s ease-in-out, box-shadow 0.1s linear;
      }
      & ~ article.ac-small {
        height: 24.5em;
        background: #fff;
      }
    }
    display: none;
  }
  article {
    background: #fff;
    margin-top: -1px;
    overflow: hidden;
    height: 0px;
    position: relative;
    z-index: 10;
    -webkit-transition: height 0.3s ease-in-out, box-shadow 0.6s linear;
    -moz-transition: height 0.3s ease-in-out, box-shadow 0.6s linear;
    -o-transition: height 0.3s ease-in-out, box-shadow 0.6s linear;
    -ms-transition: height 0.3s ease-in-out, box-shadow 0.6s linear;
    transition: height 0.3s ease-in-out, box-shadow 0.6s linear;
    p {
      line-height: 21px;
      font-size: 0.9em;
      padding: 10px 24px;
      color: #ffffff;
      background-color: #ff7fe5;
    }
  }
}
.copyright {
  text-align: center;
  padding: 2em 0 2em;
  p {
    color: #fff;
    font-size: 1em;
    line-height: 1.8em;
    a {
      color: #fff;
      &:hover {
        color: #ffd61c;
      }
    }
  }
}
@media (max-width: 1280px) {
  .main-wthree-row {
    width: 36%;
  }
  .owl-carousel.owl-theme {
    .item {
      margin: 1em 1em 0.5em;
    }
  }
  .agileits-top {
    min-height: 290px;
  }
  .agileinfo-top-time {
    padding: 1.5em 0 0;
  }
}
@media (max-width: 1024px) {
  .main-wthree-row {
    width: 46%;
  }
  .agileinfo-top-row {
    padding: 0 2em;
  }
}
@media (max-width: 900px) {
  .w3ls-bottom2 {
    padding: 0 1.5em 1.5em;
  }
  .agileinfo-top-row {
    h3 {
      font-size: 3.5em;
    }
    p {
      font-size: 1.3em;
    }
  }
  .agileits-top {
    min-height: 275px;
  }
  #txt {
    font-size: 1.8em;
  }
  .w3ls-bottom {
    padding: 3em 1em 0 0.5em;
  }
  .main {
    h2 {
      margin-right: 1em;
    }
    padding: 1.5em 0 0;
  }
  .agileinfo-top-img {
    span {
      height: 120px;
      width: 130px;
      top: -19%;
      left: -2%;
      background-size: 100%;
    }
  }
  h1 {
    font-size: 2.1em;
  }
  .main-wthree-row {
    width: 48%;
    margin: 3em auto 2em;
  }
}
@media (max-width: 800px) {
  .main-wthree-row {
    width: 57%;
  }
  .ac-container {
    input {
      &:checked {
        & ~ article.ac-small {
          height: 20em;
        }
      }
    }
  }
}
@media (max-width: 768px) {
  .agileinfo-top-img {
    span {
      height: 99px;
      width: 108px;
      top: -17%;
      left: -1%;
    }
  }
  .agileinfo-top-row {
    h3 {
      font-size: 3em;
    }
    p {
      font-size: 1em;
    }
  }
  .agileits-top {
    min-height: 240px;
  }
  .w3ls-bottom {
    padding: 2em 1em 0 0.5em;
  }
}
@media (max-width: 736px) {
  div#txt {
    font-size: 1.6em;
  }
}
@media (max-width: 667px) {
  .main-wthree-row {
    width: 60%;
  }
}
@media (max-width: 640px) {
  .main-wthree-row {
    width: 65%;
  }
  .copyright {
    padding: 1em 1em 2em;
  }
}
@media (max-width: 568px) {
  .main-wthree-row {
    width: 75%;
  }
  h1 {
    font-size: 1.9em;
  }
}
@media (max-width: 480px) {
  .main-wthree-row {
    width: 80%;
  }
  .main {
    h2 {
      margin-right: 0.5em;
      font-size: 1.1em;
    }
  }
  .agileinfo-top-row {
    h3 {
      font-size: 2.5em;
    }
    p {
      font-size: 0.9em;
    }
  }
  .w3ls-bottom2 {
    padding: 0 1.2em 1em;
  }
  .owl-carousel.owl-theme {
    .item {
      margin: 1em 1em 0;
    }
  }
  .copyright {
    p {
      font-size: 0.9em;
    }
  }
}
@media (max-width: 414px) {
  h1 {
    font-size: 1.7em;
    font-size: 1.6em;
  }
  .main {
    padding: 1em 0 0;
  }
  .main-wthree-row {
    width: 82%;
    margin: 2em auto 1em;
  }
  .agileinfo-top-row {
    h3 {
      font-size: 2.2em;
    }
  }
  .agileinfo-top-img {
    span {
      height: 83px;
      width: 90px;
      left: 0%;
    }
  }
  .agileits-top {
    min-height: 220px;
  }
  .date {
    font-size: 0.8em;
    margin-top: 0.5em;
  }
  div#txt {
    font-size: 1.5em;
  }
  .wthree-grids {
    ul {
      padding: 0.6em 0 0;
      li.wthree-img {
        img {
          width: 30px;
          height: 29px;
          margin-top: 1px;
        }
      }
    }
  }
  .ac-container {
    input {
      &:checked {
        & ~ article.ac-small {
          height: 18.2em;
        }
      }
    }
  }
  .w3ls-bottom {
    padding: 3em 1em 0 0.5em;
  }
  .owl-carousel.owl-theme {
    .item {
      margin: 0.5em 1em;
    }
  }
}
@media (max-width: 384px) {
  .agileinfo-top-img {
    span {
      top: -14%;
    }
  }
  .biseller-column {
    p {
      font-size: 0.85em;
    }
  }
  .owl-carousel.owl-theme {
    .item {
      margin: 0 1em;
    }
  }
  .w3ls-bottom {
    padding: 2.2em 1em 0 0.5em;
  }
  .ac-container {
    label {
      font-size: 0.9em;
    }
  }
}
@media (max-width: 320px) {
  h1 {
    font-size: 1.5em;
  }
  .agileinfo-top-img {
    span {
      height: 70px;
      width: 75px;
      left: 0%;
    }
  }
  .agileinfo-top-row {
    padding: 0 1.5em;
    h3 {
      font-size: 1.8em;
    }
    p {
      font-size: 0.85em;
    }
  }
  div#txt {
    font-size: 1.3em;
  }
  .date {
    font-size: 0.8em;
    margin-top: 0.2em;
  }
  .agileits-top {
    min-height: 175px;
  }
  .w3ls-bottom {
    margin-top: -2em;
  }
  .w3ls-bottom2 {
    padding: 0 1em 1em;
  }
  .main {
    h2 {
      margin-right: 0;
      font-size: 1em;
    }
  }
  .copyright {
    padding: 0.4em 1em 1em;
    p {
      font-size: 0.85em;
    }
  }
}
</style>