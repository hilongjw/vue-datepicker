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
      	time:{
      		ourtime: '',
	        option: {
	          week: ['Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa', 'Su'],
	          month: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']
	        }
      	}
      }
    },
    components: {
      'date-picker': myDatepicker
    }
}
</script>

<template>
	<div class="someclass">
		<date-picker :time.sync="time.ourtime" :option="time.option"></date-picker>
	</div>
  
</template>

```

# License

[The MIT License](http://opensource.org/licenses/MIT)

