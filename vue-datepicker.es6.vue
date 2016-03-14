<script>
import moment from 'moment'
import _ from 'lodash'
export default {
  props: {
    time: {
      type: String,
      required: true
    },
    option: {
      type: Object,
      default() {
        return {
          type: 'day',
          startsOnSunday : false,
          week: ['Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa', 'Su'],
          month: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
          format: 'YYYY-MM-DD',
          color: {
            header: '#3f51b5',
            headerText: '#fff'
          },
          inputStyle: {
            'display': 'inline-block',
            'padding': '6px',
            'line-height': '22px',
            'font-size': '16px',
            'border': '2px solid #fff',
            'box-shadow': '0 1px 3px 0 rgba(0, 0, 0, 0.2)',
            'border-radius': '2px',
            'color': '#5F5F5F'
          },
          placeholder: 'when?',
          buttons : {
            ok : 'Ok',
            cancel : 'Cancel'
          },
          overlayOpacity : 0.5,
          dismissible : true
        }
      }
    },
    limit: {
      type: Array,
      default() {
        return []
      }
    }
  },
  data() {
    function hours() {
        let list = []
        let hour = 0
        while (hour < 24) {

          list.push({
            checked: false,
            value: hour<10 ?  '0'+hour : hour
          })
          hour++
        }
        return list
      }
      function mins() {
        let list = []
        let min = 0
        while (min < 60) {
          list.push({
            checked: false,
            value: min<10 ?  '0'+min : min
          })
          min++
        }
        return list
      }
    return {
      hours:hours(),
        mins:mins(),
      showInfo: {
        hour: false,
        day: false,
        month: false,
        year: false,
        check: false
      },
      displayInfo: {
        month: '',
        weekDay : ''
      },
      library: {
        week: this.option.week,
        month: this.option.month,
        year: []
      },
      checked: {
        oldtime: '',
        currentMoment: null,
        year: '',
        month: '',
        day: '',
        hour: '00',
        min: '00'
      },
      dayList: [],
      dayMonth : null,
      is_sweeping : false,
    }
  },
  methods: {
    nextMonth(type) {
        let next = null;
        this.is_sweeping = true;
        setTimeout(function(){
          this.is_sweeping = false;
        }.bind(this),50)

        type == 'next' ? next = moment(this.checked.currentMoment).add(1, 'M') : next = moment(this.checked.currentMoment).add(-1, 'M')

        this.showDay(next)

      },
      showDay(time) {
        if (time === undefined||!Date.parse(time)) {
          this.checked.currentMoment = moment()
        } else {
            this.checked.currentMoment = moment(time, this.option.format)
        }
        this.showOne('day')

        this.checked.year = moment(this.checked.currentMoment).format("YYYY")
        this.checked.month = moment(this.checked.currentMoment).format("MM")
        this.checked.day = moment(this.checked.currentMoment).format("DD")

        this.displayInfo.month = this.library.month[moment(this.checked.currentMoment).month()];

        this.displayInfo.weekDay = this.library.week[moment(this.checked.currentMoment).day() - 1]

        let days = []
        let currentMoment = this.checked.currentMoment
        let firstDay = moment(currentMoment).date(1).day()

        //gettting previous and next month

        let currentMonth = _.cloneDeep(currentMoment);
        let previousMonth = _.cloneDeep(currentMoment)
        let nextMonth = _.cloneDeep(currentMoment)
        nextMonth.add(1,'months');
        previousMonth.subtract(1,'months');


        let monthDays = moment(currentMoment).daysInMonth()
        let oldtime = this.checked.oldtime;
        for (let i = 1; i <= monthDays; ++i) {
          days.push({
            value: i,
            inMonth: true,
            unavailable: false,
            checked: false
          })
          if (i == Math.ceil(moment(currentMoment).format("D")) && moment(oldtime).year() == moment(currentMoment).year() && moment(oldtime).month() == moment(currentMoment).month()) {
            days[i - 1].checked = true
          }
        }

        for (let i = 0; i < firstDay - (this.option.startsOnSunday? 0 : 1); i++) {
            let passiveDay = {
              value: previousMonth.daysInMonth() - (i),
              inMonth: false,
              action : 'previous',
              unavailable : false,
              checked : false
            }
            days.unshift(passiveDay);
        }

        var passiveDaysAtFinal = 42 - days.length;
        for (let i = 1; i <= passiveDaysAtFinal; i++) {
            let passiveDay = {
              value: i,
              inMonth: false,
              action : 'next',
              unavailable : false,
              checked : false
            }
            days.push(passiveDay);
        }

        if (this.limit.length > 0) {
          for (let li of this.limit) {
            switch (li.type) {
              case 'fromto':
                days = this.limitFromTo(li, days)
                break;
              case 'weekday':
                days = this.limitWeekDay(li, days)
                break;
            }
          }
        }

        this.dayList = days
      },
      limitWeekDay(limit, days) {
        days.map((day) => {
          let tday
          day.value < 10 ? tday = '0' + day.value : tday = day.value

          if (limit.available.indexOf(Math.floor(moment(this.checked.year + '-' + this.checked.month + '-' + tday).format('d'))) == -1) {
            day.unavailable = true
          }

        })
        return days
      },
      limitFromTo(limit, days) {

        days.map((day) => {

          day.value = day.value <10? '0'+day.value : day.value;
          if(day.inMonth){
            if (!moment(this.checked.year + '-' + this.checked.month + '-' + day.value).isBetween(limit.from, limit.to)) {
              day.unavailable = true
            }
          }else{
            if(day.action === 'next'){
              var _nm = parseInt(this.checked.month) + 1
              _nm = _nm < 10? '0'+_nm : _nm;

              if (!moment(this.checked.year + '-' + _nm + '-' + day.value).isBetween(limit.from, limit.to)) {
                day.unavailable = true
              }
            }else{
               var _pm = parseInt(this.checked.month) - 1
              _pm = _pm < 10? '0'+_pm : _pm;
              if (!moment(this.checked.year + '-' + _pm + '-' + day.value).isBetween(limit.from, limit.to)) {
                day.unavailable = true
              }
            }
          }
        })
        return days
      },
      checkDay(obj) {
        if (obj.unavailable || obj.value == '') {
          return false
        }

        if(!(obj.inMonth)){
          this.nextMonth(obj.action)
        }

        this.dayList.map(x => x.checked = false)
        obj.checked = true
        this.checked.day = obj.value
        this.checked.day.length < 2 ? this.checked.day = '0' + this.checked.day : this.checked.day
        if (this.option.type == 'day') {
          this.picked()
        }else{
          this.showOne('hour')
          let ctime = this.checked.year + '-' + this.checked.month + '-' + this.checked.day+' '+this.checked.hour+':'+this.checked.min
          this.dayMonth = moment(ctime, "YYYY-MM-DD HH:mm");
        }
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
            this.showInfo.hour = false
            this.showInfo.day = false
            this.showInfo.year = true
            this.showInfo.month = false
            break;
          case 'month':
            this.showInfo.hour = false
            this.showInfo.day = false
            this.showInfo.year = false
            this.showInfo.month = true
            break;
          case 'day':
            this.showInfo.hour = false
            this.showInfo.day = true
            this.showInfo.year = false
            this.showInfo.month = false
            break;
          case 'hour':
            this.showInfo.hour = true
            this.showInfo.day = false
            this.showInfo.year = false
            this.showInfo.month = false
            break;
          default:
            this.showInfo.day = true
            this.showInfo.year = false
            this.showInfo.month = false
            this.showInfo.hour = false


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

        this.dayMonth = this.checked.currentMoment;
        this.showDay(this.checked.currentMoment)
      },
      setMonth(month) {
        let mo = (this.library.month.indexOf(month) + 1);
        if (mo < 10) {
          mo = '0' + '' + mo
        }
        this.checked.currentMoment = moment(this.checked.year + '-' + mo + '-' + this.checked.day)

        this.dayMonth = this.checked.currentMoment;
        this.showDay(this.checked.currentMoment)
      },
      showCheck() {
        //avoid to open keyboard in mobile devices
        document.querySelector(".cov-datepicker").blur()

        var currentDate = null;

        if (this.time == '' || !(this.time)) {
          var nd = new Date;
          var year = nd.getFullYear();
          var month = (nd.getMonth() + 1) < 10? '0'+(nd.getMonth() + 1) : (nd.getMonth() +1 ) ;
          var day = (nd.getDate() < 10)? ('0'+nd.getDate()) : nd.getDate() ;

          currentDate = year+"-"+month+"-"+day+" 00:00"
          this.dayMonth = currentDate;
          this.checked.oldtime = currentDate
        }else{
          //If it has a defined date
          this.dayMonth = this.time;
          this.checked.oldtime = this.time
        }

        this.showDay(this.time)
        this.showInfo.check = true;
      },
      setTime(type, obj, list){
        for(let item of list){
          item.checked = false
          if(item.value == obj.value){
            item.checked = true
            this.checked[type] = item.value
          }
        }
      },
      picked(){
        let ctime = this.checked.year + '-' + this.checked.month + '-' + this.checked.day+' '+this.checked.hour+':'+this.checked.min
        this.checked.currentMoment = moment(ctime, "YYYY-MM-DD HH:mm")
        this.dayMonth = moment(ctime, "YYYY-MM-DD HH:mm");
        this.time = moment(this.checked.currentMoment).format(this.option.format)
        this.showInfo.check=false
      },
      dismiss(evt){
        if(evt.target.className === 'datepicker-overlay'){
          if(this.option.dismissible == undefined || this.option.dismissible){
            this.showInfo.check = false;
          }
        }
    }


  },
  filters:{
    getWeekdayMonth(date){
      let weekday = null;

      if(!(this.option.startsOnSunday)){
        weekday = this.option.week[moment(date).day() -1 ]
      }else{
        weekday = this.option.week[moment(date).day()]
      }

      let month = this.option.month[moment(date).month()];
      let day = moment(date).date()
      return weekday+" "+day+", "+month;
    },
    getOnlyYear(date){
      return moment(date).year();
    },
    getChar(string){
      if(string){
        return string.substring(0,1);
      }
      return '';
    },
    removeZero(number){
      return parseInt(number)
    }
  }
}
</script>
<style scoped>
.datepicker-overlay{
  position: fixed;
  width: 100%;
  height: 100%;
  z-index:998;
  top:0;
  left: 0;
  overflow: hidden;
  -webkit-animation: fadein 0.5s; /* Safari, Chrome and Opera > 12.1 */
       -moz-animation: fadein 0.5s; /* Firefox < 16 */
        -ms-animation: fadein 0.5s; /* Internet Explorer */
         -o-animation: fadein 0.5s; /* Opera < 12.1 */
            animation: fadein 0.5s;
}


@keyframes fadein {
    from { opacity: 0; }
    to   { opacity: 1; }
}

/* Firefox < 16 */
@-moz-keyframes fadein {
    from { opacity: 0; }
    to   { opacity: 1; }
}

/* Safari, Chrome and Opera > 12.1 */
@-webkit-keyframes fadein {
    from { opacity: 0; }
    to   { opacity: 1; }
}

/* Internet Explorer */
@-ms-keyframes fadein {
    from { opacity: 0; }
    to   { opacity: 1; }
}

/* Opera < 12.1 */
@-o-keyframes fadein {
    from { opacity: 0; }
    to   { opacity: 1; }
}

.cov-date-body {
  display: inline-block;
  background: #3F51B5;
  overflow-y: auto;
  position: relative;
  font-size: 16px;
  font-family: 'Roboto';
  font-weight: 400;
  position: fixed;
  display: block;
  width: 400px;
  max-width: 320px;
  z-index: 999;
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
  padding: 15px 11px;
  box-sizing: border-box !important;
  -moz-box-sizing: border-box !important;
  -webkit-box-sizing: border-box !important;
  -ms-box-sizing: border-box !important;
  width: 400px;
  max-width: 100%;
  max-height: 285px;
  text-align: start!important;
  -webkit-animation: fadein 0.5s; /* Safari, Chrome and Opera > 12.1 */
       -moz-animation: fadein 0.5s; /* Firefox < 16 */
        -ms-animation: fadein 0.5s; /* Internet Explorer */
         -o-animation: fadein 0.5s; /* Opera < 12.1 */
            animation: fadein 0.5s;

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
  height: 16.6666%;
  padding: 0;
  font-size: 13px;
  line-height: 37px;
  color: #000;
  background: #fff;
  -webkit-touch-callout: none; /* iOS Safari */
  -webkit-user-select: none;   /* Chrome/Safari/Opera */
  -khtml-user-select: none;    /* Konqueror */
  -moz-user-select: none;      /* Firefox */
  -ms-user-select: none;       /* IE/Edge */
  user-select: none;     
}

.non-selectable{
  -webkit-touch-callout: none; /* iOS Safari */
  -webkit-user-select: none;   /* Chrome/Safari/Opera */
  -khtml-user-select: none;    /* Konqueror */
  -moz-user-select: none;      /* Firefox */
  -ms-user-select: none;       /* IE/Edge */
  user-select: none;       
}

.week{
  //border-bottom: 1px solid rgba(0,0,0,0.1);
  margin: 0 -0.5em;
}

.week ul {
  margin: 0 0 8px;
  padding: 0;
  list-style: none;
}

.week ul li {
  width: 14.2857143%;
  display: inline-block;
  text-align: center;
  font-size: 13px;
  background: transparent;
  background: #eee;
  color: #aaa;
  font-weight: 500;
  padding:0.5em;
}

.passive-day{
  color: #bbb;
}
.checked {
  background: #F50057;
  color: #FFF !important;
  border-radius: 100%;
}

.unavailable {
  color: #ccc;
  cursor: not-allowed;
}

.cov-date-monthly {
  //height: 150px;
  text-align: center;
  position: relative;
  background: white;
}

.cov-date-monthly > div {
  display: inline-block;
  padding: 0;
  margin: 0;
  vertical-align: middle;
  color: #555;
  text-align: center;
  cursor: pointer;
}

.cov-date-previous{
  position: absolute;
  color: #fff!important;
  display: none;
  left: 1em;
  top: 30%;
  padding: 0 1em!important;
}

.cov-date-next {
  position: absolute;
  color: #fff!important;
  display: none;
  font-size:1em;
  right: 1em;
  top: 30%;
  padding: 0 1em!important;
}

.cov-date-labels{
  padding: 14px 24px;
  
}

.cov-date-box{
  min-height: 285px;
  background: white;
  
}

.cov-date-labels .label-year{
  color:#ccc;
  font-size: 20px;
  font-weight: 500;
}

.cov-date-labels .label-month{
  color:#fff;
  font-size: 28px;
  font-weight: 500;

}

.cov-date-caption {
  padding: 10px!important;
  box-sizing: border-box;
  font-size: 20px;
  color:#444!important;
  font-weight: 600;
}

.cov-date-caption span:hover {
  color: rgba(0, 0, 0, 0.9);
}

.cov-date-previous:hover,
.cov-date-next:hover {
  //background: rgba(0, 0, 0, 0.8);
  //padding:1em;
}

.day:hover {
  background: #EAEAEA;
  border-radius: 100%;

}

.unavailable:hover {
  background: none;
}

.checked:hover {
  background: #FF4F8E;
  border-radius: 100%;

}

.cov-date-next::before ,
.cov-date-previous::before {
  width: 12px;
  height: 2px;
  text-align: center;
  position: absolute;
  background: #777;
  top: 50%;
  margin-top: -7px;
  margin-left: -6px;
  left: 50%;
  line-height: 0;
  content: '';
  -webkit-transform: rotate(45deg);
  -moz-transform: rotate(45deg);
  transform: rotate(45deg);
}

.cov-date-next::after,
.cov-date-previous::after {
  width: 12px;
  height: 2px;
  text-align: center;
  position: absolute;
  background: #777;
  margin-top: 1px;
  margin-left: -6px;
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

.date-item {
  text-align: center;
  font-size: 20px;
  padding: 10px 0;
  cursor: pointer;
}

.date-item:hover {
  background: #e0e0e0;
  font-size: 24px;
}

.date-list {
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

.button-box {
  background: #fff;
  vertical-align: top;
  height: 50px;
  line-height: 50px;
  text-align: right;
  padding-right: 20px;
}

.button-box span {
  cursor: pointer;
  padding: 10px 20px;
}

.button-box span:hover{
  background: rgba(90,90,90,0.23);
}

.watch-box {
  min-height: 285px;
  max-height: 285px;
  //overflow: hidden;
}

.hour-box,
.min-box {
  display: inline-block;
  width: 50%;
  text-align: center;
  height: 285px;
  overflow: auto;
  float: left;
}
.hour-box ul,
.min-box ul{
  list-style: none;
    margin: 0;
    padding: 0;
}

.hour-item, .min-item {
  padding: 10px;
  font-size: 36px;
  cursor: pointer;
}
.hour-item:hover, .min-item:hover{
  background: #E3E3E3;
}
.hour-box .active, .min-box .active{
  background: #F50057;
  color: #FFF !important;
}
::-webkit-scrollbar {
    width: 2px;
}
::-webkit-scrollbar-track {
    background: #E3E3E3;
}
::-webkit-scrollbar-thumb {
    background: #C1C1C1;
    border-radius: 2px;
}
</style>
<template>
  <div class="cov-vue-date">
    <div class="datepickbox">
      <input 
      type="text"

      title="input date" 
      class="cov-datepicker" 
      placeholder="{{option.placeholder}}" 
      v-model="time" 
      @click="showCheck" 
      :style="option.inputStyle"/>

    <div class="datepicker-overlay"
      v-if="showInfo.check"
      @click="dismiss($event)"
      v-bind:style="{'background' : option.overlayOpacity? 'rgba(0,0,0,'+option.overlayOpacity+')' : 'rgba(0,0,0,0.5)'}">

      <div 
      class="cov-date-body non-selectable" 
      >

        <div class="cov-date-labels" :style="{'background-color': option.color ? option.color.header : '#3f51b5'}">
            <span class="label-year" @click="showYear"><small>{{ dayMonth | getOnlyYear}}</small></span>
            <br>
            <span class="label-month" @click="showMonth">{{ dayMonth | getWeekdayMonth}}</span>
        </div>
        <div class="cov-date-monthly">
          <div class="cov-date-previous" @click="nextMonth('pre')">«</div>
          <div class="cov-date-caption" :style="{'color': option.color ? option.color.headerText : '#fff'}">
            <span @click="showMonth"><small>{{displayInfo.month}}</small></span>
            <span @click="showYear"><small>{{checked.year}}</small></span>
          </div>
          <div class="cov-date-next" @click="nextMonth('next')">»</div>
        </div>
        <div class="cov-date-box" v-if="showInfo.day"  v-touch:swipeleft="nextMonth('next')"  v-touch:swiperight="nextMonth('prev')">
          <div class="cov-picker-box" v-if="!(is_sweeping)">
            <div class="week">
              <ul>
                <li track-by="$index" v-for="weekie in library.week">{{weekie | getChar}}</li>
              </ul>
            </div>
            <div
            class="day"
            v-for="day in dayList"
            :track-by="$index"
            @click="checkDay(day)"
            :class="{'checked':day.checked,'unavailable':day.unavailable,'passive-day': !(day.inMonth)}"
            >{{day.value | removeZero}}</div>
          </div>
        </div>
        <div class="cov-date-box list-box" v-if="showInfo.year">
          <div class="cov-picker-box date-list" id="yearList">
            <div class="date-item" v-for="yearItem in library.year" track-by="$index" @click="setYear(yearItem)">{{yearItem}}</div>
          </div>
        </div>
        <div class="cov-date-box list-box" v-if="showInfo.month">
          <div class="cov-picker-box date-list">
            <div class="date-item" v-for="monthItem in library.month" track-by="$index" @click="setMonth(monthItem)">{{monthItem}}</div>
          </div>
        </div>
        <div class="cov-date-box list-box" v-if="showInfo.hour">
          <div class="cov-picker-box date-list">
            <div class="watch-box">
              <div class="hour-box">
              <div class="mui-pciker-rule mui-pciker-rule-ft"></div>
              <ul>
                <li 
                class="hour-item" 
                v-for="hitem in hours" 
                @click="setTime('hour', hitem, hours)"
                :class="{'active':hitem.checked}"
                >{{hitem.value}}</li>
              </ul>
              </div>
              <div class="min-box">
              <div class="mui-pciker-rule mui-pciker-rule-ft"></div>
                <div 
                class="min-item" 
                v-for="mitem in mins" 
                @click="setTime('min',mitem, mins)"
                :class="{'active':mitem.checked}"
                >{{mitem.value}}</div>
              </div>
            </div>
          </div>
        </div>
        <div class="button-box">
          <span class="cal-buttons" @click="showInfo.check=false">{{option.buttons? option.buttons.cancel : 'Cancel' }}</span>
          <span class="cal-buttons" @click="picked">{{option.buttons? option.buttons.ok : 'Ok'}}</span>
        </div>
      </div>

    </div>
  </div>
</template>