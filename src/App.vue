<template>
  <div id="app" class="container">
    <br><br>
    <div class="mb-4">
      <div id="examplePopoverButton" class="el-date-editor el-range-editor el-input__inner el-date-editor--daterange el-range-editor--default el-tooltip__trigger el-tooltip__trigger" aria-describedby="el-id-8217-44">
        <i class="el-icon el-input__icon el-range__icon">
          <svg class="icon" width="200" height="200" viewBox="0 0 1024 1024" xmlns="http://www.w3.org/2000/svg"><path fill="currentColor" d="M128 384v512h768V192H768v32a32 32 0 11-64 0v-32H320v32a32 32 0 01-64 0v-32H128v128h768v64H128zm192-256h384V96a32 32 0 1164 0v32h160a32 32 0 0132 32v768a32 32 0 01-32 32H96a32 32 0 01-32-32V160a32 32 0 0132-32h160V96a32 32 0 0164 0v32zm-32 384h64a32 32 0 010 64h-64a32 32 0 010-64zm0 192h64a32 32 0 110 64h-64a32 32 0 110-64zm192-192h64a32 32 0 010 64h-64a32 32 0 010-64zm0 192h64a32 32 0 110 64h-64a32 32 0 110-64zm192-192h64a32 32 0 110 64h-64a32 32 0 110-64zm0 192h64a32 32 0 110 64h-64a32 32 0 110-64z"></path></svg>
        </i>
          <input autocomplete="off" name="" placeholder="Start date" class="el-range-input" :value="startDate | dateFormat">
          <span class="el-range-separator">To</span>
          <input autocomplete="off" name="" placeholder="End date" class="el-range-input" :value="endDate | dateFormat">
          <i class="el-icon el-input__icon el-range__close-icon" v-if="areDatesSet" :click="clearDates">
            <svg class="icon" width="200" height="200" viewBox="0 0 1024 1024" xmlns="http://www.w3.org/2000/svg"><path fill="currentColor" d="M466.752 512l-90.496-90.496a32 32 0 0145.248-45.248L512 466.752l90.496-90.496a32 32 0 1145.248 45.248L557.248 512l90.496 90.496a32 32 0 11-45.248 45.248L512 557.248l-90.496 90.496a32 32 0 01-45.248-45.248L466.752 512z"></path><path fill="currentColor" d="M512 896a384 384 0 100-768 384 384 0 000 768zm0 64a448 448 0 110-896 448 448 0 010 896z"></path></svg>
          </i>
      </div>
      <div class="compared-to" v-if="compare">
        <span class="compared-to-label">Compared to: </span>
        <span class="compared-to-start">{{ startDateCompare | dateFormat }}</span>
        <span class="compared-to-range"> - </span>
        <span class="compared-to-end">{{ endDateCompare | dateFormat }}</span>
      </div>
      <b-popover ref="examplePopover" target="examplePopoverButton" triggers="click" placement="bottom">
        <date-range-picker v-on:submit="submittedPopover" v-on:cancel="cancelledPopover" />
      </b-popover>
    </div>
  </div>
</template>

<script>
import moment from 'moment'
import { library } from '@fortawesome/fontawesome-svg-core'
import { faCalendarAlt, faCaretDown } from '@fortawesome/free-solid-svg-icons'
import BModalDirective from 'bootstrap-vue/es/directives/modal/modal'
import BPopover from 'bootstrap-vue/es/components/popover/popover'
import DateRangePicker from './components/DateRangePicker'

library.add(faCalendarAlt, faCaretDown)

export default {
  components: { DateRangePicker, BPopover },
  directives: { 'b-modal': BModalDirective },
  data: () => {
    return {
      startDate: '',
      endDate: '',
      startDateCompare: moment.utc().subtract(1, 'month').startOf('month'),
      endDateCompare: moment.utc().subtract(1, 'month').endOf('month').startOf('day'),
      compare: false
    }
  },
  methods: {
    clearDates: function() {
      this.startDate = ''
      this.endDate = ''
      this.startDateCompare = ''
      this.endDateCompare = ''
      this.compare = false
    },
    submitted: function(range) {
      console.log(range)
    },
    // Modal methods
    submittedModal: function(range) {
      this.startDate = range.startDate
      this.endDate = range.endDate
      this.closeModal()
    },
    cancelledModal: function() {
      this.closeModal()
    },
    closeModal: function() {
      this.$refs.exampleModal.hide()
    },
    // Popover methods
    submittedPopover: function(range) {
      this.startDate = range.startDate
      this.endDate = range.endDate
      this.compare = range.compare
      this.startDateCompare = range.startDateCompare
      this.endDateCompare = range.endDateCompare
      this.closePopover()
    },
    cancelledPopover: function() {
      this.closePopover()
    },
    closePopover: function() {
      this.$refs.examplePopover.$emit('close')
    }
  },
  computed: {
    areDatesSet: function() {
      if (this.startDate !== '' && this.endDate !== '') {
        return false
      } else {
        return false
      }
    }
  },
  filters: {
    dateFormat: function(value) {
      return value ? value.format('YYYY-MM-DD') : ''
    }
  }
}
</script>

<style>
@import '~bootstrap/dist/css/bootstrap.min.css';

.popover {
  max-width: 800px;
  box-shadow: rgba(0,0,0,0.1) 0px 2px 12px 0px;
  border-radius: 4px !important;
  border: 1px solid rgb(228, 231, 237);
}

.el-range-editor {
  width: 350px !important;
}

.el-range-editor.is-active {
  border-color: #409eff;
}

.el-range-editor.el-input__inner {
    display: inline-flex;
    align-items: center;
    padding: 3px 10px;
}

.el-input__inner {
    -webkit-appearance: none;
    background-color: #fff;
    background-image: none;
    border-radius: 4px;
    border: 1px solid #dcdfe6;
    box-sizing: border-box;
    color: rgb(96, 98, 102);
    display: inline-block;
    font-size: inherit;
    height: 32px;
    line-height: 32px;
    outline: none;
    padding: 0 11px;
    /* transition: var(--el-transition-border); */
    width: 100%;
}

.el-date-editor .el-range__icon {
    height: inherit;
    font-size: 14px;
    color: #dcdfe6;
    float: left;
}

.el-range__close-icon {
  color: #dcdfe6;
}

.el-icon {
    color: #dcdfe6;
    height: 1em;
    width: 1em;
    line-height: 1em;
    display: inline-flex;
    justify-content: center;
    align-items: center;
    position: relative;
    fill: currentColor;
    color: var(--color);
    font-size: inherit;
}

.el-range-editor .el-range-input {
    line-height: 1;
}

.el-date-editor .el-range-input {
    appearance: none;
    border: none;
    outline: none;
    display: inline-block;
    height: 100%;
    margin: 0;
    padding: 0;
    width: 39%;
    text-align: center;
    font-size: var(--el-font-size-base);
    color: var(--el-text-color-regular);
}

.compared-to {
  line-height: 22px;
  font-size: 12px;
}
.compared-to .compared-to-label {
  color: rgb(96, 98, 102);
  margin-right: 5px;
}
.compared-to .compared-to-range {
  margin: 0px 6px;
}
</style>
