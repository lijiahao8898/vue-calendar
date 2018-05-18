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
                this.toYear = year
                await this.init();
                this.$refs.calendar.style.marginTop = (window.innerHeight - this.$refs.calendar.clientHeight) / 2 + 'px';
            });
        },
        mounted () {
        },
        methods: {
            init () {
                const firstDay = new Date(this.year, this.month, 1);
                const dayLength = this.getDayCounts();
                const start = (firstDay.getDay() || 7) - 1;
                const arr = [];
                let n = 1;
                for (let i = 0; i < (dayLength + start); i++) {
                    const d = new Date(this.year, this.month, n);
                    if (i < start) {
                        arr[i] = ({
                            day: '',
                            week: '',
                            active: false
                        });
                    } else {
                        arr[i] = ({
                            day: d.getDate(),
                            week: d.getDay(),
                            active: this.isActive(d, this.today, i)
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
            isActive (d, today, i) {
                if (d.getDate() === today.getDate() && this.month === this.toMonth && this.year === this.toYear) {
                    return true;
                }
                return false;
            },
            getDayCounts () {
                let d = this.dateTime;
                return new Date(d.getFullYear(), (d.getMonth() + 1), 0).getDate();
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
