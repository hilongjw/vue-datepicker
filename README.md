# vue-datepicker
calendar and datepicker component with material design for Vue.js

# Demo

The demo page is [HERE](http://hilongjw.github.io/vue-datepicker/demo.html).

![Screenshot](screenshot.png)

# Requirements

- [Vue.js](https://github.com/yyx990803/vue) `^1.0.0`
- [moment](https://github.com/moment/moment) `^2.11.1`

# Instllation

## npm

```shell
$ npm install vue-datepicker
```

# Usage

```html
<!-- your component -->
<script>
import myDatepicker from 'vue-datepicker'
export default {
  data() {
      return {
        starttime: '',
        endtime: '2016-01-19',
        option: {
          week: ['Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa', 'Su'],
          month: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
          format:'YYYY-MM-DD'
        },
        limit: {
          type:'fromto',
          from:'2016-01-10',
          to:'2016-01-30'
        }
      }
    },
    components: {
      'date-picker': myDatepicker
    }
}
</script>
<style>
.card {
  text-align: center;
  background: #8E24AA;
  margin-top: 200px;
  padding: 50px;
  color: #fff;
}

.row {
  margin-bottom: 1rem;
}
.test{
  text-align: center;
}
</style>
<template>
  <div class="card">
    <div class="row">
      <span>Departure Date：</span>
      <date-picker :time.sync="starttime" :option="option" :limit="limit"></date-picker>
    </div>
    <div class="row">
      <span>Return Date：</span>
      <date-picker :time.sync="endtime" :option="option"></date-picker>
    </div>
  </div>
  <div class="test">
    Departure Date:{{starttime}}
    <br> 
    Return Date：{{endtime}}
  </div>
</template>

```

# API


 - limit

*from sometime to sometime

```javascript
limit: {
  type:'fromto',
  from:'2016-01-10',
  to:'2016-01-30'
}
```
*weekdays

```javascript
limit:{
  type: 'weekday',
  available: [1,2,3,4,5] 
}
```javascript

prop

```html
<date-picker :time.sync="starttime" :limit="limit"></date-picker>
```

# License

[The MIT License](http://opensource.org/licenses/MIT)

