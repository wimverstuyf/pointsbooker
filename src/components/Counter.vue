<template>
    <swipe class="my-swipe" v-bind:continuous="swipeOptions.continuous" v-bind:auto="swipeOptions.auto" v-bind:defaultIndex="defaultScreen" v-bind:showIndicators="swipeOptions.showIndicators">
          <swipe-item>
              <div class="counter">
                <br/>
                <button class="submit-btn submit-reset" v-on:click="clearAll">Wis alle gegevens !</button>

                <br/><br/><br/>
                <button class="submit-btn" v-on:click="reload">Herladen</button>
              </div>
          </swipe-item>
          <swipe-item v-for="day in days">
            <div class="counter">
                <div class="date">
                    <div class="day">{{ day.description }}</div>
                    <div class="cheat" v-if="day.isCheatDay">VRIJE DAG</div>
                </div>

                <div v-show="!day.isCheatDay">
                    <div class="count">
                        <label>Resterend:</label>
                        <div class="value">{{ day.remaining }}</div>
                    </div>

                    <div class="action">
                        <button class="submit-btn" v-on:click="addPoint" v-if="day.now">Gebruik punt</button>
                    </div>

                    <div class="extra" v-if="day.now">
                      Extra punten: {{ cache.extra }}
                    </div>
                </div>
            </div>
          </swipe-item>
    </swipe>
</template>

<script>
require('vue-swipe/dist/vue-swipe.css');
import { Swipe, SwipeItem } from 'vue-swipe';

export default {
  name: "Counter",
  components: {
    Swipe,
    SwipeItem
  },
  props: {
    points: Number,
    extra: Number,
    cheat: Number
  },
  data: function() {
    return {
        swipeOptions: {
            continuous: false,
            auto: 10000000,
            showIndicators: false
        },
        timer: null,
        update_count: 0,
        current_day: -1,
        cache: {
            week: null,
            extra: 0,
            days: {}
        },
        options: {
            days: ['Maandag', 'Dinsdag', 'Woensdag', 'Donderdag', 'Vrijdag', 'Zaterdag', 'Zondag']
        }
    };
  },
  created: function() {
    if (!this.timer) {
        this.timer = setInterval(this.restoreFromCache, 30000);
    }
  },
  computed: {
    remaining: function() {
        this.update_count; // to trigger update
        var date = this.selectedDate();
        this.buildCache(date);
        return this.cache.days[this.getWeekDay(date)];
    },
    dayDescription: function() {
        var day = this.getWeekDay(this.selectedDate());
        if (day == this.getWeekDay(new Date())) {
            return 'Vandaag';
        } else {
            return this.options.days[day-1];
        }
    },
    defaultScreen: function() {
        if (this.current_day == -1) {
            return this.getWeekDay(new Date());
        } else {
            return this.current_day;
        }
    },
    days: function() {
        this.update_count; // to trigger update
        this.buildCache(new Date());

        var today = this.getWeekDay(new Date());

        var result = {};
        for(let day=1; day<=today; day++) {
            result[day] = {
                description: today==day?"Vandaag":this.options.days[day-1],
                now: today==day,
                isCheatDay: this.cheat==day,
                remaining: this.cache.days[day],
            };
        }

        return result;
    }
  },
  methods: {
    clearAll: function() {
        localStorage.clear();
        location.reload();
    },
    reload: function() {
      location.reload();
    },
    storeToCache: function() {
        localStorage.counter_week = this.cache.week;
        localStorage.counter_extra = this.cache.extra;
        localStorage.counter_day1 = this.cache.days[1];
        localStorage.counter_day2 = this.cache.days[2];
        localStorage.counter_day3 = this.cache.days[3];
        localStorage.counter_day4 = this.cache.days[4];
        localStorage.counter_day5 = this.cache.days[5];
        localStorage.counter_day6 = this.cache.days[6];
        localStorage.counter_day7 = this.cache.days[7];
    },
    restoreFromCache: function() {
        this.update_count += 1;
        if (localStorage.counter_week) {
            this.cache.week = localStorage.counter_week;
            this.cache.extra = localStorage.counter_extra*1;
            this.cache.days = {
                1: localStorage.counter_day1*1,
                2: localStorage.counter_day2*1,
                3: localStorage.counter_day3*1,
                4: localStorage.counter_day4*1,
                5: localStorage.counter_day5*1,
                6: localStorage.counter_day6*1,
                7: localStorage.counter_day7*1
            };
        }
    },
    addPoint: function() {
        var date = this.selectedDate();
        this.buildCache(date);
        var week = this.getWeekNumber(date);
        var day = this.getWeekDay(date);

        if (this.cache.days[day] <= 0) {
            this.cache.extra -= 1;
        }
        this.cache.days[day] -= 1;
        this.update_count += 1; // to trigger update

        this.storeToCache();
    },
    buildCache(date) {
        var week = this.getWeekNumber(date);
        if (this.cache.week == null) {
            this.restoreFromCache();
        }
        if (this.cache.week != week) {
            this.cache.week = week;
            this.cache.extra = this.extra;
            this.cache.days = {
                1: this.points,
                2: this.points,
                3: this.points,
                4: this.points,
                5: this.points,
                6: this.points,
                7: this.points
            };

            this.storeToCache();
        }
    },
    selectedDate: function() {
        var now_day = this.getWeekDay(new Date());
        if (this.current_day == -1 || this.current_day > now_day) {
            this.current_day = now_day;
        }

        var date = new Date();
        date.setDate(date.getDate() - (this.current_day - this.getWeekDay(date)));
        return date;
    },
    getWeekDay: function(d) {
        var day = d.getDay();

        if (day == 0) {
            day = 7;
        }
        return day;
    },
    getWeekNumber: function(d) {
        d = new Date(Date.UTC(d.getFullYear(), d.getMonth(), d.getDate()));
        d.setUTCDate(d.getUTCDate() + 4 - (d.getUTCDay()||7));
        var yearStart = new Date(Date.UTC(d.getUTCFullYear(),0,1));
        var weekNo = Math.ceil(( ( (d - yearStart) / 86400000) + 1)/7);
        return d.getUTCFullYear()+'-'+weekNo;
    }
  }
};
</script>

<style lang="scss" src="../assets/styles/Counter.scss"/>
