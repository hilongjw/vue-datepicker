<script>
import moment from 'moment'
export default {
  props: {
    time: {
      type: String,
      required: true
    },
    option: {
      type: Object,
      default: function() {
        return {
          week: ['Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa', 'Su'],
          month: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']
        }
      }
    }
  },
  data() {
    return {
      showInfo: {
        day: false,
        month: false,
        year: false,
        check: false
      },
      displayInfo: {
        month: ''
      },
      library: {
        week: this.option.week,
        month: this.option.month,
        year: []
      },
      checked: {
        currentMoment: null,
        year: '',
        month: '',
        day: '',
      },
      dayList: []
    }
  },
  methods: {
    nextMonth(type) {
        let next = null;

        if (type == 'next') {
          next = moment(this.checked.currentMoment).add(1, 'M')
        } else {
          next = moment(this.checked.currentMoment).add(-1, 'M')
        }

        this.showDay(next)
      },
      showDay(time) {
        if (time === undefined) {
          this.checked.currentMoment = moment()
        } else {
          this.checked.currentMoment = moment(time)
        }
        this.showOne('day')


        this.checked.year = moment(this.checked.currentMoment).format("YYYY")
        this.checked.month = moment(this.checked.currentMoment).format("MM")
        this.checked.day = moment(this.checked.currentMoment).format("DD")

        this.displayInfo.month = this.library.month[moment(this.checked.currentMoment).month()];

        let days = []
        let currentMoment = time
        let firstDay = moment(currentMoment).date(1).day()
        let monthDays = moment(currentMoment).daysInMonth()

        for (let i = 1; i <= monthDays; ++i) {
          days.push({
            value: i,
            inMonth: true,
            checked: false
          })
          if (i == Math.ceil(moment(currentMoment).format("D")) && moment(currentMoment).month() == moment().month() && moment(currentMoment).year() == moment().year()) {
            days[i - 1].checked = true
          }
        }



        for (let i = 0; i < firstDay - 1; i++) {
          days.unshift({
            value: '',
            inMonth: false
          })
        }


        this.dayList = days

      },
      checkDay(obj) {
        this.dayList.map(x => x.checked = false)
        obj.checked = true
        this.checked.day = obj.value
        this.time = this.checked.year + '-' + this.checked.month + '-' + this.checked.day
        this.showInfo.check = false
      },
      showYear() {
        let year = moment(this.checked.currentMoment).year()
        this.library.year = []
        let yearTmp = []
        for (let i = year - 100; i < year + 5; ++i) {
          yearTmp.push(i)
        }
        this.library.year = yearTmp

        this.showOne('year')

        let self = this
        this.$nextTick(function() {
          let listDom = document.getElementById('yearList');
          listDom.scrollTop = (listDom.scrollHeight - 100)
          self.addYear()
        })
      },
      showOne(type) {
        switch (type) {
          case 'year':
            this.showInfo.day = false
            this.showInfo.year = true
            this.showInfo.month = false
            break;
          case 'month':
            this.showInfo.day = false
            this.showInfo.year = false
            this.showInfo.month = true
            break;
          case 'day':
            this.showInfo.day = true
            this.showInfo.year = false
            this.showInfo.month = false
            break;
          default:
            this.showInfo.day = true
            this.showInfo.year = false
            this.showInfo.month = false

        }
      },
      showMonth() {
        this.showOne('month')
      },
      addYear() {
        let self = this;
        let listDom = document.getElementById('yearList');
        let tmp = 0;
        listDom.addEventListener('scroll', function(e) {

          if (listDom.scrollTop < listDom.scrollHeight - 100) {
            let len = self.library.year.length
            let lastYear = self.library.year[len - 1]
            self.library.year.push(lastYear + 1)
          }


        }, false)
      },
      setYear(year) {
        this.checked.currentMoment = moment(year + '-' + this.checked.month + '-' + this.checked.day)
        this.showDay(this.checked.currentMoment)
      },
      setMonth(month) {
        let mo = (this.library.month.indexOf(month) + 1);
        if (mo < 10) {
          mo = '0' + '' + mo
        }
        this.checked.currentMoment = moment(this.checked.year + '-' + mo + '-' + this.checked.day)
        this.showDay(this.checked.currentMoment)
      },
      showCheck() {
        this.time=='' ? this.showDay() : this.showDay(this.time) 
        this.showInfo.check = true;
      }


  }
}
</script>
<style scoped>
.cov-date-body {
  display: inline-block;
  background: #3F51B5;
  overflow: hidden;
  position: relative;
  font-size: 16px;
  font-family: 'Roboto';
  font-weight: 400;
  position: fixed;
  display: block;
  width: 400px;
  top: 50%;
  left: 50%;
  -webkit-transform: translate(-50%, -50%);
  -ms-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.2);
}

.cov-picker-box {
  background: #fff;
  width: 100%;
  display: inline-block;
  padding: 25px;
  box-sizing: border-box !important;
  -moz-box-sizing: border-box !important;
  -webkit-box-sizing: border-box !important;
  -ms-box-sizing: border-box !important;
  width: 400px;
  height: 280px;
  text-align: start!important;
}

.cov-picker-box td {
  height: 34px;
  width: 34px;
  padding: 0;
  line-height: 34px;
  color: #000;
  background: #fff;
  text-align: center;
  cursor: pointer;
}

.cov-picker-box td:hover {
  background: #E6E6E6;
}

table {
  border-collapse: collapse;
  border-spacing: 0;
  width: 100%;
}

.day {
  width: 14.2857143%;
  display: inline-block;
  text-align: center;
  cursor: pointer;
  height: 34px;
  padding: 0;
  line-height: 34px;
  color: #000;
  background: #fff;
}

.week ul {
  margin: 0;
  padding: 0;
  list-style: none;
}

.week ul li {
  width: 14.2%;
  display: inline-block;
  text-align: center;
  background: transparent;
  color: #000;
  font-weight: bold;
}

.checked {
  background: #F50057;
  color: #FFF !important;
  border-radius: 3px;
}

.cov-date-monthly {
  height: 150px;
}

.cov-date-monthly > div {
  display: inline-block;
  padding: 0;
  margin: 0;
  vertical-align: middle;
  color: #fff;
  height: 150px;
  float: left;
  text-align: center;
  cursor: pointer;
}

.cov-date-previous,
.cov-date-next {
  position: relative;
  width: 20% !important;
  text-indent: -300px;
  overflow: hidden;
  color: #fff;
}

.cov-date-caption {
  width: 60%;
  padding: 50px 0!important;
  box-sizing: border-box;
  font-size: 24px;
}

.cov-date-caption span:hover {
  color: rgba(255, 255, 255, 0.7);
}

.cov-date-previous:hover,
.cov-date-next:hover {
  background: rgba(255, 255, 255, 0.1);
}

.day:hover {
  background: #EAEAEA;
}

.checked:hover {
  background: #FF4F8E;
}

.cov-date-next::before,
.cov-date-previous::before {
  width: 20px;
  height: 2px;
  text-align: center;
  position: absolute;
  background: #fff;
  top: 50%;
  margin-top: -7px;
  margin-left: -7px;
  left: 50%;
  line-height: 0;
  content: '';
  -webkit-transform: rotate(45deg);
  -moz-transform: rotate(45deg);
  transform: rotate(45deg);
}

.cov-date-next::after,
.cov-date-previous::after {
  width: 20px;
  height: 2px;
  text-align: center;
  position: absolute;
  background: #fff;
  margin-top: 6px;
  margin-left: -7px;
  top: 50%;
  left: 50%;
  line-height: 0;
  content: '';
  -webkit-transform: rotate(-45deg);
  -moz-transform: rotate(-45deg);
  transform: rotate(-45deg);
}

.cov-date-previous::after {
  -webkit-transform: rotate(45deg);
  -moz-transform: rotate(45deg);
  transform: rotate(45deg);
}

.cov-date-previous::before {
  -webkit-transform: rotate(-45deg);
  -moz-transform: rotate(-45deg);
  transform: rotate(-45deg);
}

.year-item {
  text-align: center;
  font-size: 20px;
  padding: 10px 0;
  cursor: pointer;
}

.year-item:hover {
  background: #e0e0e0;
}

.year-list {
  overflow: auto;
  vertical-align: top;
  padding: 0;
}

.cov-datepicker {
  display: inline-block;
  padding: 6px;
  line-height: 22px;
  font-size: 16px;
  border: 2px solid #fff;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.2);
  border-radius: 2px;
  font-family: 'Roboto';
  color: #5F5F5F;
}

.cov-vue-date {
  display: inline-block;
  color: #5D5D5D;
}
</style>
<template>
  <div class="cov-vue-date">
    <div class="datepickbox">
      <input type="text" title="input date" class="cov-datepicker" placeholder="When?" v-model="time" @click="showCheck" />
    </div>
    <div class="cov-date-body" v-if="showInfo.check">
      <div class="cov-date-monthly">
        <div class="cov-date-previous" @click="nextMonth('pre')">«</div>
        <div class="cov-date-caption">
          <span @click="showYear"><small>{{checked.year}}</small></span>
          <br>
          <span @click="showMonth">{{displayInfo.month}}</span>
        </div>
        <div class="cov-date-next" @click="nextMonth('next')">»</div>
      </div>
      <div class="cov-date-box" v-if="showInfo.day">
        <div class="cov-picker-box">
          <div class="week">
            <ul>
              <li v-for="weekie in library.week">{{weekie}}</li>
            </ul>
          </div>
          <div class="day" v-for="day in dayList" track-by="$index" @click="checkDay(day)" :class="{'checked':day.checked}">{{day.value}}</div>
        </div>
      </div>
      <div class="cov-date-box year-box" v-if="showInfo.year">
        <div class="cov-picker-box year-list" id="yearList">
          <div class="year-item" v-for="yearItem in library.year" track-by="$index" @click="setYear(yearItem)">{{yearItem}}</div>
        </div>
      </div>
      <div class="cov-date-box year-box" v-if="showInfo.month">
        <div class="cov-picker-box year-list">
          <div class="year-item" v-for="monthItem in library.month" track-by="$index" @click="setMonth(monthItem)">{{monthItem}}</div>
        </div>
      </div>
    </div>
  </div>
</template>
