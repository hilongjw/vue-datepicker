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
        testTime: '',
        option: {
          type: 'day',
          week: ['Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa', 'Su'],
          month: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
          format:'YYYY-MM-DD'
          placeholder: 'when?',
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
          color: {
            header: '#ccc',
            headerText: '#f00'
          },
          buttons : {
            ok : 'Ok',
            cancel : 'Cancel'
          },
          overlayOpacity : 0.5, //0.5 as default
          dismissible : true //as true as default
        },
        timeoption: {
          type: 'min',
          week: ['Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa', 'Su'],
          month: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
          format:"YYYY-MM-DD HH:mm"
        },
        limit:[{
            type: 'weekday',
            available: [1,2,3,4,5]
          },
          {
           type:'fromto',
           from:'2016-02-01',
           to:'2016-02-20'
          }]
      }
    },
    components: {
      'date-picker': myDatepicker
    }
}
</script>
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
    <div class="row">
      <span>time：</span>
      <date-picker :time.sync="testTime" :option="timeoption"></date-picker>
    </div>
  </div>
  <div class="test">
    Departure Date:{{starttime}}
    <br> Return Date：{{endtime}}
    <br> test Date：{{testTime}}
  </div>
  <input type="time">
</template>


```

# API


 - limit

 * from sometime to sometime

```javascript

limit: {
  type:'fromto',
  from:'2016-01-10',
  to:'2016-01-30'
}

```
 * weekdays

```javascript

limit:{
  type: 'weekday',
  available: [1, 2, 3, 4, 5] 
}

```

prop

```html

<date-picker :time.sync="starttime" :limit="limit"></date-picker>

```

# License

[The MIT License](http://opensource.org/licenses/MIT)

