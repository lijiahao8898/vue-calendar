<template>
    <div id="calendar" class="calendar">
        <div class="calendar-title">
            <p><span class="pre" @click="goPre">pre</span>{{year}} - {{month + 1}}<span class="next" @click="goNext">next</span>
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
                today: '',
                toMonth: '',
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
            date () {
                return this.dateTime.getDate();
            }
        },
        created () {
            this.$nextTick(() => {
                const year = this.year;
                const month = this.month;
                const date = this.date;
                this.today = new Date(year, month, date);
                this.toMonth = month;
                this.init();
            });
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
                console.log(this.month, this.dateArr);
            },
            goPre () {
                this.dateTime = new Date(this.dateTime.getFullYear(), this.month - 1, 1);
                this.init();
                console.log(this.month);
            },
            goNext () {
                this.dateTime = new Date(this.dateTime.getFullYear(), this.month + 1, 1);
                this.init();
                console.log(this.month);
            },
            isActive (d, today) {
                return d.getDate() === today.getDate() && this.month === this.toMonth? true : false;
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
            }
            .next {
                display: inline-block;
                float: right;
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
