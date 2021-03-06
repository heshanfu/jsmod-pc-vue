<template>
  <a v-bind:style="[_style, customStyle]"
      @mousedown="isPress = true"
      @mouseup="isPress = false"
      @mouseenter="isHover = true"
      @mouseleave="() => {isHover = false; isPress = false}"
      v-bind:class="buttonClass"
      v-bind:href="href"
      @click="_onClick"
      >

    <mod-spin v-if="status == 'loading'" class="jsmod-button-spin"></mod-spin>

    <span v-if="status != 'loading'" class="jsmod-button-text"><slot></slot></span>
    <span v-else class="jsmod-button-text-loading"><slot name="loading"></slot></span>
  </a>
</template>

<script>
  import Vue from 'vue';
  import { ModSpin } from '../spin';

  const BUTTON_STATES = [
    'default',
    'loading',
    'disabeld'
  ];

  export default {
    components: {
      ModSpin
    },

    computed: {
      buttonClass () {
        let arr = ['jsmod-button'];

        this.inline && arr.push('jsmod-button-inline');
        this.status == 'disabeld' && arr.push('jsmod-button-disabled');
        // 都有 border
        arr.push('jsmod-button-border');
        (this.status != 'disabeld' && this.isPress) && arr.push('jsmod-button-pressing');
        (this.status != 'disabeld' && this.isHover && !this.isPress) && arr.push('jsmod-button-hover');

        this.class && arr.push(this.class);

        return arr;
      },

      _style () {
        let obj = {};

        if (this.color) {
          obj.color = this.color;
        }

        if (this.backgroundColor) {
          obj.backgroundColor = this.backgroundColor;
        }

        if (this.borderColor) {
          obj.borderColor = this.borderColor;
        } else if (this.customStyle && this.customStyle.backgroundColor) {
          obj.borderColor = this.customStyle.backgroundColor;
        } else {
          obj.borderColor = this.backgroundColor;
        }

        return obj;
      }
    },

    methods: {
      _onClick (e) {
        if (this.status != 'loading' && this.status != 'disabeld') {
          this.$emit('click');
          this.onClick(e);
        }
      }
    },

    data () {
      return {
        isPress: false,
        isHover: false
      }
    },

    props: {
      status: {
        type: String,
        validator (value) {
          return BUTTON_STATES.indexOf(value) > -1;
        },
        default: 'default'
      },

      backgroundColor: {
        type: String,
        default: '#108ee9'
      },

      color: {
        type: String
      },

      borderColor: {
        type: [Boolean, String]
      },

      inline: {
        type: Boolean,
        default: false
      },

      onClick: {
        type: Function,
        default: () => {}
      },

      href: {
        type: [String, Object],
        default: 'javascript:void(0)'
      },

      customStyle: {
        type: Object
      }
    }

  }
</script>


<style lang="stylus">
  @import "../../styles/mixin";

  .jsmod-button
    display: block;
    padding: 8px 10px;
    text-align: center;
    background-color: link-color;
    border-radius: 5px;
    color: #fff;
    position: relative;
    overflow: hidden;

    .jsmod-button-spin
      vertical-align: text-bottom;

    .jsmod-button-text,
    .jsmod-button-text-loading
      position: relative;
      z-index: 1;


    .jsmod-button-icon
      vertical-align: middle;
      position: relative;
      z-index: 1;

      i
        font-size: 18px;

    &.jsmod-button-inline
      display: inline-block;

    &.jsmod-button-disabled
      color: #bbb!important;
      background-color: #ddd!important;
      border-color: #ddd!important;

    &.jsmod-button-border
      border: 1px solid border-color;

    &.jsmod-button-hover
      &:before
        content: "";
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        background-color: rgba(0, 0, 0, 0.1);

    &.jsmod-button-pressing
      &:before
        content: "";
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        background-color: rgba(0, 0, 0, 0.2);


</style>
