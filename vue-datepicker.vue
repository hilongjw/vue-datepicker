<script>
'use strict';

Object.defineProperty(exports, '__esModule', {
  value: true
});
var _temporalUndefined = {};

function _interopRequireDefault(obj) {
  return obj && obj.__esModule ? obj : { 'default': obj };
}

function _temporalAssertDefined(val, name) {
  var undef = _temporalUndefined;
  if (val === undef) {
    throw new ReferenceError(name + ' is not defined - temporal dead zone');
  }
  return true;
}

var _moment = require('moment');

var _moment2 = _interopRequireDefault(_moment);

exports['default'] = {
  props: {
    time: {
      type: String,
      required: true
    },
    option: {
      type: Object,
      'default': function _default() {
        return {
          type: 'day',
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
          placeholder: 'when?'
        };
      }
    },
    limit: {
      type: Array,
      'default': function _default() {
        return [];
      }
    }
  },
  data: function() {
    function hours() {
      var list = _temporalUndefined;
      var hour = _temporalUndefined;
      list = [];
      hour = 24;
      while ((_temporalAssertDefined(hour, 'hour') && hour) > 0) {
        _temporalAssertDefined(hour, 'hour'), hour--;

        (_temporalAssertDefined(list, 'list') && list).push({
          checked: false,
          value: (_temporalAssertDefined(hour, 'hour') && hour) < 10 ? '0' + (_temporalAssertDefined(hour, 'hour') && hour) : _temporalAssertDefined(hour, 'hour') && hour
        });
      }
      return _temporalAssertDefined(list, 'list') && list;
    }
    function mins() {
      var list = _temporalUndefined;
      var min = _temporalUndefined;
      list = [];
      min = 60;
      while ((_temporalAssertDefined(min, 'min') && min) > 0) {
        _temporalAssertDefined(min, 'min'), min--;
        (_temporalAssertDefined(list, 'list') && list).push({
          checked: false,
          value: (_temporalAssertDefined(min, 'min') && min) < 10 ? '0' + (_temporalAssertDefined(min, 'min') && min) : _temporalAssertDefined(min, 'min') && min
        });
      }
      return _temporalAssertDefined(list, 'list') && list;
    }
    return {
      hours: hours(),
      mins: mins(),
      showInfo: {
        hour: false,
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
        oldtime: '',
        currentMoment: null,
        year: '',
        month: '',
        day: '',
        hour: '00',
        min: '00'
      },
      dayList: []
    };
  },
  methods: {
    nextMonth: function(type) {
      var next = _temporalUndefined;

      next = null;
      type == 'next' ? (_temporalAssertDefined(next, 'next'), next = (0, _moment2['default'])(this.checked.currentMoment).add(1, 'M')) : (_temporalAssertDefined(next, 'next'), next = (0, _moment2['default'])(this.checked.currentMoment).add(-1, 'M'));

      this.showDay(_temporalAssertDefined(next, 'next') && next);
    },
    showDay: function showDay(time) {

      var days = _temporalUndefined;
      var currentMoment = _temporalUndefined;
      var firstDay = _temporalUndefined;
      var monthDays = _temporalUndefined;
      var oldtime = _temporalUndefined;

      if (time === undefined || !Date.parse(time)) {
        this.checked.currentMoment = (0, _moment2['default'])();
      } else {
        this.checked.currentMoment = (0, _moment2['default'])(time, this.option.format);
      }
      this.showOne('day');

      this.checked.year = (0, _moment2['default'])(this.checked.currentMoment).format("YYYY");
      this.checked.month = (0, _moment2['default'])(this.checked.currentMoment).format("MM");
      this.checked.day = (0, _moment2['default'])(this.checked.currentMoment).format("DD");

      this.displayInfo.month = this.library.month[(0, _moment2['default'])(this.checked.currentMoment).month()];days = [];
      currentMoment = this.checked.currentMoment;
      firstDay = (0, _moment2['default'])(_temporalAssertDefined(currentMoment, 'currentMoment') && currentMoment).date(1).day();
      monthDays = (0, _moment2['default'])(_temporalAssertDefined(currentMoment, 'currentMoment') && currentMoment).daysInMonth();
      oldtime = this.checked.oldtime;
      for (var i = 1; i <= (_temporalAssertDefined(monthDays, 'monthDays') && monthDays); ++i) {
        (_temporalAssertDefined(days, 'days') && days).push({
          value: _temporalAssertDefined(i, 'i') && i,
          inMonth: true,
          unavailable: false,
          checked: false
        });
        if ((_temporalAssertDefined(i, 'i') && i) == Math.ceil((0, _moment2['default'])(_temporalAssertDefined(currentMoment, 'currentMoment') && currentMoment).format("D")) && (0, _moment2['default'])(_temporalAssertDefined(oldtime, 'oldtime') && oldtime).year() == (0, _moment2['default'])(_temporalAssertDefined(currentMoment, 'currentMoment') && currentMoment).year() && (0, _moment2['default'])(_temporalAssertDefined(oldtime, 'oldtime') && oldtime).month() == (0, _moment2['default'])(_temporalAssertDefined(currentMoment, 'currentMoment') && currentMoment).month()) {
          (_temporalAssertDefined(days, 'days') && days)[(_temporalAssertDefined(i, 'i') && i) - 1].checked = true;
        }
      }

      for (var i = 0; i < (_temporalAssertDefined(firstDay, 'firstDay') && firstDay) - 1; i++) {
        (_temporalAssertDefined(days, 'days') && days).unshift({
          value: '',
          inMonth: false
        });
      }

      if (this.limit.length > 0) {
        var _iteratorNormalCompletion = true;
        var _didIteratorError = false;
        var _iteratorError = undefined;

        try {
          for (var _iterator = this.limit[Symbol.iterator](), _step; !(_iteratorNormalCompletion = (_step = _iterator.next()).done); _iteratorNormalCompletion = true) {
            var li = _temporalUndefined;
            li = _step.value;

            switch ((_temporalAssertDefined(li, 'li') && li).type) {
              case 'fromto':
                _temporalAssertDefined(_temporalAssertDefined(days, 'days') && days, 'days');

                days = this.limitFromTo(_temporalAssertDefined(li, 'li') && li, _temporalAssertDefined(_temporalAssertDefined(days, 'days') && days, 'days') && _temporalAssertDefined(days, 'days') && days);

                break;
              case 'weekday':
                _temporalAssertDefined(_temporalAssertDefined(days, 'days') && days, 'days');

                days = this.limitWeekDay(_temporalAssertDefined(li, 'li') && li, _temporalAssertDefined(_temporalAssertDefined(days, 'days') && days, 'days') && _temporalAssertDefined(days, 'days') && days);

                break;
            }
          }
        } catch (err) {
          _didIteratorError = true;
          _iteratorError = err;
        } finally {
          try {
            if (!_iteratorNormalCompletion && _iterator['return']) {
              _iterator['return']();
            }
          } finally {
            if (_didIteratorError) {
              throw _iteratorError;
            }
          }
        }
      }

      this.dayList = _temporalAssertDefined(days, 'days') && days;
    },
    limitWeekDay: function(limit, days) {
      var _this = this;

      days.map(function (day) {
        var tday = _temporalUndefined;
        tday = undefined;
        day.value < 10 ? (_temporalAssertDefined(tday, 'tday'), tday = '0' + day.value) : (_temporalAssertDefined(tday, 'tday'), tday = day.value);

        if (limit.available.indexOf(Math.floor((0, _moment2['default'])(_this.checked.year + '-' + _this.checked.month + '-' + (_temporalAssertDefined(tday, 'tday') && tday)).format('d'))) == -1) {
          day.unavailable = true;
        }
      });
      return days;
    },
    limitFromTo: function(limit, days) {
      var _this2 = this;

      days.map(function (day) {
        if (!(0, _moment2['default'])(_this2.checked.year + '-' + _this2.checked.month + '-' + day.value).isBetween(limit.from, limit.to)) {
          day.unavailable = true;
        }
      });
      return days;
    },
    checkDay: function(obj) {
      if (obj.unavailable) {
        return false;
      }
      this.dayList.map(function (x) {
        return x.checked = false;
      });
      obj.checked = true;
      this.checked.day = obj.value;
      this.checked.day.length < 2 ? this.checked.day = '0' + this.checked.day : this.checked.day;
      if (this.option.type == 'day') {
        this.picked();
      } else {
        this.showOne('hour');
      }
    },

    showYear: function() {
      var year = _temporalUndefined;

      var yearTmp = _temporalUndefined;

      var self = _temporalUndefined;
      year = (0, _moment2['default'])(this.checked.currentMoment).year();
      this.library.year = [];yearTmp = [];
      for (var i = (_temporalAssertDefined(year, 'year') && year) - 100; i < (_temporalAssertDefined(year, 'year') && year) + 5; ++i) {
        (_temporalAssertDefined(yearTmp, 'yearTmp') && yearTmp).push(_temporalAssertDefined(i, 'i') && i);
      }
      this.library.year = _temporalAssertDefined(yearTmp, 'yearTmp') && yearTmp;

      this.showOne('year');self = this;
      this.$nextTick(function () {
        var listDom = _temporalUndefined;
        listDom = document.getElementById('yearList');
        (_temporalAssertDefined(listDom, 'listDom') && listDom).scrollTop = (_temporalAssertDefined(listDom, 'listDom') && listDom).scrollHeight - 100;
        (_temporalAssertDefined(self, 'self') && self).addYear();
      });
    },
    showOne: function(type) {
      switch (type) {
        case 'year':
          this.showInfo.hour = false;
          this.showInfo.day = false;
          this.showInfo.year = true;
          this.showInfo.month = false;
          break;
        case 'month':
          this.showInfo.hour = false;
          this.showInfo.day = false;
          this.showInfo.year = false;
          this.showInfo.month = true;
          break;
        case 'day':
          this.showInfo.hour = false;
          this.showInfo.day = true;
          this.showInfo.year = false;
          this.showInfo.month = false;
          break;
        case 'hour':
          this.showInfo.hour = true;
          this.showInfo.day = false;
          this.showInfo.year = false;
          this.showInfo.month = false;
          break;
        default:
          this.showInfo.day = true;
          this.showInfo.year = false;
          this.showInfo.month = false;
          this.showInfo.hour = false;

      }
    },
    showMonth: function() {
      this.showOne('month');
    },
    addYear: function() {
      var self = _temporalUndefined;
      var listDom = _temporalUndefined;
      var tmp = _temporalUndefined;
      self = this;
      listDom = document.getElementById('yearList');
      tmp = 0;
      (_temporalAssertDefined(listDom, 'listDom') && listDom).addEventListener('scroll', function (e) {

        if ((_temporalAssertDefined(listDom, 'listDom') && listDom).scrollTop < (_temporalAssertDefined(listDom, 'listDom') && listDom).scrollHeight - 100) {
          var len = _temporalUndefined;
          var lastYear = _temporalUndefined;
          len = (_temporalAssertDefined(self, 'self') && self).library.year.length;
          lastYear = (_temporalAssertDefined(self, 'self') && self).library.year[(_temporalAssertDefined(len, 'len') && len) - 1];
          (_temporalAssertDefined(self, 'self') && self).library.year.push((_temporalAssertDefined(lastYear, 'lastYear') && lastYear) + 1);
        }
      }, false);
    },
    setYear: function(year) {
      this.checked.currentMoment = (0, _moment2['default'])(year + '-' + this.checked.month + '-' + this.checked.day);
      this.showDay(this.checked.currentMoment);
    },
    setMonth: function(month) {
      var mo = _temporalUndefined;
      mo = this.library.month.indexOf(month) + 1;
      if ((_temporalAssertDefined(mo, 'mo') && mo) < 10) {
        _temporalAssertDefined(_temporalAssertDefined(mo, 'mo') && mo, 'mo');

        mo = '0' + '' + (_temporalAssertDefined(_temporalAssertDefined(mo, 'mo') && mo, 'mo') && _temporalAssertDefined(mo, 'mo') && mo);
      }
      this.checked.currentMoment = (0, _moment2['default'])(this.checked.year + '-' + (_temporalAssertDefined(mo, 'mo') && mo) + '-' + this.checked.day);
      this.showDay(this.checked.currentMoment);
    },
    showCheck: function() {
      if (this.time == '') {
        this.showDay();
      } else {
        this.checked.oldtime = this.time;
        this.showDay(this.time);
      }

      this.showInfo.check = true;
    },
    setTime: function(type, obj, list) {
      var _iteratorNormalCompletion2 = true;
      var _didIteratorError2 = false;
      var _iteratorError2 = undefined;

      try {
        for (var _iterator2 = list[Symbol.iterator](), _step2; !(_iteratorNormalCompletion2 = (_step2 = _iterator2.next()).done); _iteratorNormalCompletion2 = true) {
          var item = _temporalUndefined;
          item = _step2.value;

          (_temporalAssertDefined(item, 'item') && item).checked = false;
          if ((_temporalAssertDefined(item, 'item') && item).value == obj.value) {
            (_temporalAssertDefined(item, 'item') && item).checked = true;
            this.checked[type] = (_temporalAssertDefined(item, 'item') && item).value;
          }
        }
      } catch (err) {
        _didIteratorError2 = true;
        _iteratorError2 = err;
      } finally {
        try {
          if (!_iteratorNormalCompletion2 && _iterator2['return']) {
            _iterator2['return']();
          }
        } finally {
          if (_didIteratorError2) {
            throw _iteratorError2;
          }
        }
      }
    },
    picked: function() {
      var ctime = _temporalUndefined;
      ctime = this.checked.year + '-' + this.checked.month + '-' + this.checked.day + ' ' + this.checked.hour + ':' + this.checked.min;
      this.checked.currentMoment = (0, _moment2['default'])(_temporalAssertDefined(ctime, 'ctime') && ctime, "YYYY-MM-DD HH:mm");
      this.time = (0, _moment2['default'])(this.checked.currentMoment).format(this.option.format);
      this.showInfo.check = false;
    }

  }
};
module.exports = exports['default'];
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
  max-width: 100%;
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
  max-width: 100%;
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
  vertical-align: middle;
}

.week ul {
  margin: 0 0 8px;
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

.unavailable {
  color: #ccc;
  cursor: not-allowed;
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

.unavailable:hover {
  background: none;
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

.date-item {
  text-align: center;
  font-size: 20px;
  padding: 10px 0;
  cursor: pointer;
}

.date-item:hover {
  background: #e0e0e0;
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

.watch-box {
  height: 100%;
  overflow: hidden;
}

.hour-box,
.min-box {
  display: inline-block;
  width: 50%;
  text-align: center;
  height: 100%;
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
    </div>
    <div 
    class="cov-date-body" 
    :style="{'background-color': option.color ? option.color.header : '#3f51b5'}"
    v-if="showInfo.check">
      <div class="cov-date-monthly">
        <div class="cov-date-previous" @click="nextMonth('pre')">«</div>
        <div class="cov-date-caption" :style="{'color': option.color ? option.color.headerText : '#fff'}">
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
          <div
          class="day"
          v-for="day in dayList"
          track-by="$index"
          @click="checkDay(day)"
          :class="{'checked':day.checked,'unavailable':day.unavailable}"
          >{{day.value}}</div>
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
        <span @click="showInfo.check=false">Cancel</span>
        <span @click="picked">OK</span>
      </div>
    </div>
  </div>
</template>