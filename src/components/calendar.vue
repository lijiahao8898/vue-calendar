<template>
    <div id="calendar" class="calendar" ref="calendar">
        <div id="weather"></div>
        <div class="calendar-title">
            <p><span class="pre" @click="goPre">&lt;</span>{{year}} - {{monthText}}<span class="next" @click="goNext">&gt;</span>
            </p>
        </div>
        <div class="calendar-content">
            <ul>
                <li v-for="(item, index) in weeks" :key="index">
                    <span>{{item}}</span>
                </li>
            </ul>
            <ul>
                <li v-for="(item, index) in dateArr" :key="index" :class="{active: item.active ? item.active : false}">
                    <span>{{item.day}}</span>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
    export default {
        data () {
            return {
                dateTime: new Date(),
                today: '',                  // 当天
                toMonth: '',                // 当天的月份
                toYear: '',                 // 当天的年份
                dateArr: [],
                weeks: ['一', '二', '三', '四', '五', '六', '日']
            };
        },
        computed: {
            year () {
                return this.dateTime.getFullYear();
            },
            month () {
                return this.dateTime.getMonth();
            },
            monthText () {
                return (this.month + 1).toString().length < 2 ? '0' + (this.month + 1) : this.month + 1;
            },
            date () {
                return this.dateTime.getDate();
            }
        },
        created () {
            this.$nextTick(async () => {
                const year = this.year;
                const month = this.month;
                const date = this.date;
                this.today = new Date(year, month, date);
                this.toMonth = month;
                this.toYear = year;
                await this.init();
                this.findWeather();
                console.log(this.$refs.calendar);
                this.$refs.calendar.style.marginTop = (window.innerHeight - this.$refs.calendar.clientHeight) / 2 + 'px';
            });
        },
        mounted () {
        },
        methods: {
            init () {
                const firstDay = new Date(this.year, this.month, 1);
                const dayLength = this.getDayCounts();
                const start = firstDay.getDay() || 6;
                const arr = [];
                let n = 1;
                for (let i = 0; i < (dayLength + start); i++) {
                    const d = new Date(this.year, this.month, i + 1 - start - firstDay.getDay());
                    if (i < start) {
                        arr[i] = ({
                            day: '',
                            week: '',
                            active: false
                        });
                    } else {
                        arr[i] = ({
                            day: n,
                            week: d.getDay(),
                            active: this.isActive(d, this.today)
                        });
                        n++;
                    }
                }
                this.dateArr = arr;
            },
            goPre () {
                this.dateTime = new Date(this.dateTime.getFullYear(), this.month - 1, 1);
                this.init();
            },
            goNext () {
                this.dateTime = new Date(this.dateTime.getFullYear(), this.month + 1, 1);
                this.init();
            },
            isActive (d, today) {
                return d.getDate() === today.getDate() && this.month === this.toMonth && this.year === this.toYear ? true : false;
            },
            getDayCounts () {
                let d = this.dateTime;
                return new Date(d.getFullYear(), (d.getMonth() + 1), 0).getDate();
            },
            findWeather () {
                const cityUrl = 'http://int.dpool.sina.com.cn/iplookup/iplookup.php?format=js';
                console.log(cityUrl);
                this.$axios.get(cityUrl)
                    .then(function (response) {
                    })
                    .catch(function (error) {
                    });

//                $.getScript(cityUrl, function (script, textStatus, jqXHR) {
//                    var citytq = remote_ip_info.city;// 获取城市
//                    var url = 'http://php.weather.sina.com.cn/iframe/index/w_cl.php?code=js&city=' + citytq + '&day=0&dfc=3';
//                    $.ajax({
//                        url: url,
//                        dataType: 'script',
//                        scriptCharset: 'gbk',
//                        success: function (data) {
//                            var _w = window.SWther.w[citytq][0];
//                            var _f = _w.f1 + '_0.png';
//                            if (new Date().getHours() > 17) {
//                                _f = _w.f2 + '_1.png';
//                            }
//                            var img = '<img width=\'16px\' height=\'16px\' src=\'http://i2.sinaimg.cn/dy/main/weather/weatherplugin/wthIco/20_20/' + _f
//                                + '\' />';
//                            var tq = citytq + ' ' + img + ' ' + _w.s1 + ' ' + _w.t1 + '℃～' + _w.t2 + '℃ ' + _w.d1 + _w.p1 + '级';
//
//                            $('#weather').html(tq);
//                        }
//                    });
//                });
            }
        }
    };
</script>

<style lang="scss" type="text/scss" scoped>
    .calendar {
        display: inline-block;
        background: #fff;
        margin: 0 auto;
        padding: 20px;
        border-radius: 5px;
        box-shadow: 5px 20px 10px rgba(0, 0, 0, 0.2);
        &-title {
            overflow: hidden;
            .pre {
                display: inline-block;
                float: left;
                cursor: pointer;
                margin-left: -10px;
                width: 50px;
                font-weight: bold;
            }
            .next {
                display: inline-block;
                float: right;
                cursor: pointer;
                margin-right: -10px;
                width: 50px;
                font-weight: bold;
            }
        }
        &-content {
            ul {
                margin: 0 auto;
                width: 280px;
                padding-left: 0;
                text-align: left;
                li {
                    display: inline-block;
                    width: 40px;
                    height: 40px;
                    line-height: 40px;
                    text-decoration: none;
                    &.active {
                        background: #000;
                        color: #fff;
                        border-radius: 100%;
                        font-weight: bold;
                    }
                    span {
                        text-align: center;
                        display: block;
                    }
                }
            }
        }
    }
</style>
