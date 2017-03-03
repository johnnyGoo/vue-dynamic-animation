<template>
    <div :transition="active?transition:''" :class="active?transition:''">
        <slot></slot>
    </div>
</template>


/*!
* vue-dynamic-animation v0.0.1 (https://github.com/johnnyGoo/vue-dynamic-animation)
* Author: Johnny chen
*
* Copyright 2013-2015 Johnny chen
*/
<script>


    var count = 0;

    if (!window.Smart) {
        throw 'vue-popup required smart.js'
    }
    var Smart = window.Smart;
    var Css = Smart.Css;
    var Event = Smart.Event;
    var _ = Smart._;


    function defaultAnimate() {
        return {
            'animation-duration': '1s',
            'animation-timing-function': 'cubic-bezier(0.36,0.07,0.19,0.97)',
            'animation-iteration-count': 'infinite',
            'animation-direction': 'alternate'
        }
    }
    function defaultNormal() {
        return {transition: 'all 0.5s ease', opacity: 1, 'transition-delay': '0.01s'}
    }
    function defaultLeave() {
        return {transition: 'all 0.5s ease', opacity: 0, 'transition-delay': '0.01s'}
    }


    // 注册
    export default {
        // 声明 props
        props: {
            normal: {
                type: Object,
                coerce: function (val) {
                    return _.extend(defaultNormal(), val);
                },
                default: function () {
                    return {};
                }
            },
            active: {
                type: Boolean,
                default: true
            },
            enter: {
                type: Object, coerce: function (val) {
                    return _.extend(defaultLeave(), val);
                },
                default: function () {
                    return {};
                }
            }, leave: {
                type: Object, coerce: function (val) {
                    return _.extend(defaultLeave(), val);
                },
                default: function () {
                    return {};
                }
            }, type: {
                type: String,
                default: 'transition'
            }, keyframes: {
                type: Object,
                default: function () {
                    return {
                        '0%': {opacity: 1},
                        '100%': {opacity: 0}
                    }
                }
            }, animation: {
                type: Object,
                coerce: function (val) {
                    return _.extend( defaultAnimate(),val);
                },
                default: function () {
                    return {}
                }
            }
        },
        data: function () {
            count++;
            return {
                transition: 'vue-dynamic-animation-count' + count,
            }
        },
        methods: {
            _on_end: function () {
                console.log(this.transition)
            }
        },
        computed: {},
        ready: function () {
            if (this.type == 'transition') {
                if (_.isEqual({}, this.enter) && _.isEqual({}, this.leave)) {
                    Css.smartCss(this.$el, this.normal, 'px');
                    return;
                }
                if (_.isEqual({}, this.enter)) {
                    this.enter = _.clone(this.leave);
                } else if (_.isEqual({}, this.leave)) {
                    this.leave = _.clone(this.enter);
                }
                Css.createSmartCssStyle('.' + this.transition + '-transition', _.clone(this.normal), 'px');
                Css.createSmartCssStyle('.' + this.transition + '-leave', _.clone(this.leave), 'px');
                Css.createSmartCssStyle('.' + this.transition + '-enter', _.clone(this.enter), 'px');
            } else if (this.type == 'animation') {
                var objj = Smart.Utils.deepClone(this.keyframes);
                var anim = Smart.Animations.create(objj);
                this.animation['animation-name'] = anim.name;
                Smart.Css.createSmartCssStyle('.' + this.transition, this.animation, 'px');

            }
        }
    }


</script>