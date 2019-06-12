<template>
    <div id="app">
        <div class="main" v-if="current" @click.once="play()">
            <div class="main-wthree-row">
                <div class="agileits-top">
                    <div class="agileinfo-top-row">
                        <div class="agileinfo-top-img">
                            <span></span>
                        </div>
                        <h3>{{ current.temp_c }}<sup class="degree">°</sup><span>C</span></h3>
                        <p>{{ location.name }}</p>
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
                    <h2>Hôm nay</h2>
                    <carousel :items="6" :autoplay="true" :loop="false" :nav="false" :dots="false">
                        <div class="item" v-for="item in hour" :key="item.time">
                            <div class="biseller-column">
                                <p>{{ item.time_epoch | date('HH:mm') }}</p>
                                <a class="lightbox">
                                    <img :src="'https:'+item.condition.icon" :title="item.condition.text" />
                                </a>
                                <p>{{ item.temp_c }}<sup class="degree">°</sup><span>C</span></p>
                            </div>
                        </div>
                    </carousel>
                </div>
                <div class="w3ls-bottom2">
                    <div class="ac-container">
                        <input id="ac-1" name="accordion-1" type="checkbox" />
                        <label for="ac-1" class="grid1">Những ngày tới</label>
                        <article class="ac-small">
                            <div class="wthree-grids">
                                <div class="wthree-grids-row" v-for="item in day" :key="item.date_epoch">
                                    <ul class="top">
                                        <li>{{ item.date_epoch | date('YYYY-MM-DD') }}</li>
                                        <li class="wthree-img"><img :src="'https:'+item.day.condition.icon" :title="item.day.condition.text" /></li>
                                        <li class="wthree-temp">{{ item.day.mintemp_c }} <sup class="degree">°</sup><span>C</span></li>
                                        <li class="wthree-temp">{{ item.day.maxtemp_c }} <sup class="degree">°</sup><span>C</span></li>
                                    </ul>
                                    <div class="clear"> </div>
                                </div>
                            </div>
                        </article>
                    </div>
                </div>
            </div>
        </div>
        <audio src="/background.mp3" autoplay loop ref="audio"></audio>
    </div>
</template>
<script>
import carousel from 'vue-owl-carousel'
import moment from 'moment'
import axios from 'axios'

export default {
    name: 'app',
    data: () => ({
        current: null,
        location: null,
        day: [],
        hour: [],
        date: '',
        time: '',
    }),
    created() {
        setInterval(() => {
            moment.locale('vi');
            this.time = moment().format('HH:mm:ss');
            this.date = moment().format('dddd, DD MMMM gggg');
        }, 1);

        axios.get('https://geoip-db.com/json/').then(geo => {
            axios.all([
                axios.get(`https://api.apixu.com/v1/forecast.json`, {
                    params: {
                        key: process.env.VUE_APP_API_KEY,
                        q: geo.data.city,
                        lang: 'vi',
                        days: '10'
                    }
                }),
                axios.get(`https://api.apixu.com/v1/history.json`, {
                    params: {
                        key: process.env.VUE_APP_API_KEY,
                        q: geo.data.city,
                        lang: 'vi',
                        dt: moment().format('YYYY-MM-DD')
                    }
                })
            ]).then(axios.spread((forecast, history) => {
                this.current = forecast.data.current;
                this.location = forecast.data.location;
                this.day = forecast.data.forecast.forecastday;
                this.hour = history.data.forecast.forecastday[0].hour;
            }));
        })
    },
    methods: {
        play() {
            this.$refs.audio.play();
        },
    },
    filters: {
        date(value, format) {
            return moment.unix(value).format(format);
        },
    },
    components: {
        carousel
    }
}
</script>
<style lang="scss">
html, body, div, span, applet, object, iframe, h1, h2, h3, h4, h5, h6, p, blockquote, pre, a, abbr, acronym, address, big, cite, code, del, dfn, em, img, ins, kbd, q, s, samp, small, strike, strong, sub, sup, tt, var, b, u, i, dl, dt, dd, ol {
    margin: 0;
    padding: 0;
    border: 0;
    font-size: 100%;
    font: inherit;
    vertical-align: baseline;
}

body {
    font-family: 'Open Sans', sans-serif;
    background: url('./assets/main.jpg') no-repeat 0px 0px;
    background-attachment: fixed;
    -webkit-background-size: cover;
    -moz-background-size: cover;
    -o-background-size: cover;
    -ms-background-size: cover;
    background-size: cover;
}

nav {

    ul,
    li {
        margin: 0;
        padding: 0;
        border: 0;
        font-size: 100%;
        font: inherit;
        vertical-align: baseline;
    }
}

sup {
  position: static
}

fieldset, form, label, legend, table, caption, tbody, tfoot, thead, tr, th, td, article, aside, canvas, details, embed, figure, figcaption, footer, header, hgroup, menu, nav, output, ruby, section, summary, time, mark, audio, video {
    margin: 0;
    padding: 0;
    border: 0;
    font-size: 100%;
    font: inherit;
    vertical-align: baseline;
}

article, aside, details, figcaption, figure, footer, header, hgroup, menu, nav, section {
    display: block;
}

ol,
ul {
    list-style: none;
    margin: 0px;
    padding: 0px;
}

blockquote,
q {
    quotes: none;
}

blockquote {

    &:before,
    &:after {
        content: '';
        content: none;
    }
}

q {

    &:before,
    &:after {
        content: '';
        content: none;
    }
}

table {
    border-collapse: collapse;
    border-spacing: 0;
}

/*--start editing from here--*/

a {
    text-decoration: none;
}

.txt-rt {
    text-align: right;
}

/* text align right */

.txt-lt {
    text-align: left;
}

/* text align left */

.txt-center {
    text-align: center;
}

/* text align center */

.float-rt {
    float: right;
}

/* float right */

.float-lt {
    float: left;
}

/* float left */

.clear {
    clear: both;
}

/* clear float */

.pos-relative {
    position: relative;
}

/* Position Relative */

.pos-absolute {
    position: absolute;
}

/* Position Absolute */

.vertical-base {
    vertical-align: baseline;
}

/* vertical align baseline */

.vertical-top {
    vertical-align: top;
}

/* vertical align top */

nav {
    &.vertical ul li {
        display: block;
    }

    &.horizontal ul li {
        display: inline-block;
    }
}

/* vertical menu */
/* horizontal menu */

img {
    max-width: 100%;
}

/*-- main --*/

.main h2 {
    font-size: 1.2em;
    color: #00bbb4;
    text-align: right;
    margin-right: 2em;
    font-family: 'Lora', serif;
}

.main-wthree-row {
    width: 32%;
    margin: 3.5em auto 2em;
    background: #fff;
}

/*-- agileits-top --*/

.agileits-top {
    background: url('./assets/top.jpg') no-repeat 0px 0px;
    background-size: cover;
    min-height: 310px;
}

.agileinfo-top-row {
    position: relative;
    padding: 0 3em;
}

.agileinfo-top-img span {
    background: url('./assets/logo.png') no-repeat;
    background-size: cover;
    display: block;
    height: 139px;
    width: 150px;
    position: absolute;
    top: -15%;
    left: -1%;
}

.agileinfo-top-row {
    h3 {
        font-size: 4em;
        color: #FFD61C;
        padding-top: 0.2em;
        text-align: right;

        span {
            font-size: .5em;
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

.agileinfo-top-time {
    padding: 2.5em 0 0;
}

#txt {
    color: #fff;
    font-size: 2em;
    font-weight: 200;
    letter-spacing: 3px;
    font-family: 'Lora', serif;
}

.date {
    font-size: 0.9em;
    color: #ffd61c;
    margin-top: 0.5em;
    font-weight: 200;
}

/*-- //agileits-top --*/

.w3ls-bottom {
    background: url('./assets/bottom.png') no-repeat -1px 0px;
    background-size: 103%;
    padding: 3em 1em 0;
    margin-top: -4em;
}

.biseller-column p {
    font-size: 0.9em;
    color: #999;
    font-weight: 400;
}

.main a.lightbox {
    display: block;
}

.biseller-column:hover p {
    color: #00bbb4;
}

/*-- carousel --*/

.owl-carousel.owl-theme .item {
    padding: 15px 0px;
    margin: 1em 1em;
    color: #FFF;
    -webkit-border-radius: 3px;
    -moz-border-radius: 3px;
    -o-border-radius: 3px;
    -ms-border-radius: 3px;
    border-radius: 3px;
    text-align: center;
}

.customNavigation {
    text-align: center;
}

/*-- //carousel --*/

.w3ls-bottom2 {
    padding: 0 2em 2em;
}

.wthree-grids ul {
    padding: 0.8em 0 0;
    display: flex;
    align-items: center;

    &.top {
        border: none;
    }

    li {
        display: inline-block;
        width: 25%;
        line-height: 2em;
        font-size: .9em;
        color: #888;

        &.wthree-img {
            text-align: center;

            img {
                width: 35px;
                height: 35px;
            }
        }
    }
}

.wthree-temp {
    text-align: right;
}

/*-- weak-accordion --*/

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
    }

    input:checked+label {
        color: #00bbb4;

        &:hover {
            color: #00bbb4;
        }
    }

    label:after {
        content: '';
        position: absolute;
        width: 15px;
        height: 11px;
        right: 13px;
        top: 15px;
        background: url('./assets/arrow_up.png') no-repeat -4px -5px;
        -webkit-transition: .5s all;
        -moz-transition: .5s all;
        -o-transition: .5s all;
        -ms-transition: .5s all;
        transition: .5s all;
    }

    input {
        &:checked+label {
            &:hover:after {
                content: '';
                position: absolute;
                width: 15px;
                height: 11px;
                right: 13px;
                top: 15px;
                background: url('./assets/arrow_up.png') no-repeat -4px -5px;
                -webkit-transition: .5s all;
                -moz-transition: .5s all;
                -o-transition: .5s all;
                -ms-transition: .5s all;
                transition: .5s all;
                color: #000;
                -webkit-transform: rotate(180deg);
                -moz-transform: rotate(180deg);
                -o-transform: rotate(180deg);
                -ms-transform: rotate(180deg);
                transform: rotate(180deg);
                top: 10px;
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
            color: #FFFFFF;
            background-color: #FF7FE5;
        }
    }

    input:checked~article {
        -webkit-transition: height 0.5s ease-in-out, box-shadow 0.1s linear;
        -moz-transition: height 0.5s ease-in-out, box-shadow 0.1s linear;
        -o-transition: height 0.5s ease-in-out, box-shadow 0.1s linear;
        -ms-transition: height 0.5s ease-in-out, box-shadow 0.1s linear;
        transition: height 0.5s ease-in-out, box-shadow 0.1s linear;
        background-color: #E259C6;

        &.ac-small {
            height: 24.5em;
            background: #fff;
        }
    }
}

/*-- //weak-accordion --*/

/*-- //main --*/
/*-- copyright --*/

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

/*-- //copyright --*/
/*-- responsive-design --*/
@media (max-width: 1280px) {
    .main-wthree-row {
        width: 36%;
    }

    .owl-carousel.owl-theme .item {
        margin: 1em 1em 0.5em;
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

    .main h2 {
        margin-right: 1em;
    }

    .agileinfo-top-img span {
        height: 120px;
        width: 130px;
        top: -19%;
        left: -2%;
        background-size: 100%;
    }

    h1 {
        font-size: 2.1em;
    }

    .main {
        padding: 1.5em 0 0;
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

    .ac-container input:checked~article.ac-small {
        height: 20em;
    }
}

@media (max-width: 768px) {
    .agileinfo-top-img span {
        height: 99px;
        width: 108px;
        top: -17%;
        left: -1%;
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

    .main h2 {
        margin-right: 0.5em;
        font-size: 1.1em;
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

    .owl-carousel.owl-theme .item {
        margin: 1em 1em 0;
    }

    .copyright p {
        font-size: 0.9em;
    }
}

@media (max-width: 414px) {
    h1 {
        font-size: 1.7em;
    }

    .main {
        padding: 1em 0 0;
    }

    .main-wthree-row {
        width: 82%;
        margin: 2em auto 1em;
    }

    .agileinfo-top-row h3 {
        font-size: 2.2em;
    }

    .agileinfo-top-img span {
        height: 83px;
        width: 90px;
        left: 0%;
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

    .wthree-grids ul {
        li.wthree-img img {
            width: 30px;
            height: 29px;
            margin-top: 1px;
        }

        padding: 0.6em 0 0;
    }

    .ac-container input:checked~article.ac-small {
        height: 18.2em;
    }

    .w3ls-bottom {
        padding: 3em 1em 0 0.5em;
    }

    .owl-carousel.owl-theme .item {
        margin: 0.5em 1em;
    }

    h1 {
        font-size: 1.6em;
    }
}

@media (max-width: 384px) {
    .agileinfo-top-img span {
        top: -14%;
    }

    .biseller-column p {
        font-size: 0.85em;
    }

    .owl-carousel.owl-theme .item {
        margin: 0 1em;
    }

    .w3ls-bottom {
        padding: 2.2em 1em 0 0.5em;
    }

    .ac-container label {
        font-size: 0.9em;
    }
}

@media (max-width: 320px) {
    h1 {
        font-size: 1.5em;
    }

    .agileinfo-top-img span {
        height: 70px;
        width: 75px;
        left: 0%;
    }

    .agileinfo-top-row {
        h3 {
            font-size: 1.8em;
        }

        p {
            font-size: 0.85em;
        }

        padding: 0 1.5em;
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

    .main h2 {
        margin-right: 0;
        font-size: 1em;
    }

    .copyright {
        p {
            font-size: 0.85em;
        }

        padding: 0.4em 1em 1em;
    }
}
</style>