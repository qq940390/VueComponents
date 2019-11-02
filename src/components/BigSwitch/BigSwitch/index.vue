<template>
    <div class="big-switch" ref="switch">
        <div class="tel-icon" ref="dragDom">
            <svg-icon icon-class="phone"></svg-icon>
        </div>
        <div class="cover" ref="cover"></div>
        <div class="txt">
            <span v-for="(item, key) in words" :key="'quick-jump' + key" ref="jumps">
                <span :class="{'ani': shows[key].show===true}" :style="{'transition': `opacity ${(effectTime/750).toFixed(1)}s`}">{{item}}</span>
            </span>
        </div>
        <div class="cylinderbg"></div>
    </div>
</template>

<script>
import Vue from 'vue'

function hasClass (ele, cls) {
    return !!ele.className.match(new RegExp('(\\s|^)' + cls + '(\\s|$)'))
}

/**
 * Add class to element
 * @param {HTMLElement} elm
 * @param {string} cls
 */
function addClass (ele, cls) {
    if (!hasClass(ele, cls)) ele.className += ' ' + cls
}

/**
 * Remove class from element
 * @param {HTMLElement} elm
 * @param {string} cls
 */
function removeClass (ele, cls) {
    if (hasClass(ele, cls)) {
        const reg = new RegExp('(\\s|^)' + cls + '(\\s|$)')
        ele.className = ele.className.replace(reg, ' ')
    }
}

export default {
    name: 'BigSwitch',
    data () {
        return {
            switchDom: null,
            dragDom: null,
            switchWidth: 0,
            touchX: 0,
            clickX: 0,
            dragPercent: 0,
            isDragging: false,
            words: Object.assign({}, this.tip.split('')),
            total: Object.keys(Object.assign({}, this.tip.split(''))).length,
            shows: Array.apply(null, Array(15)).map(() => { return { show: false } })
        }
    },
    mounted () {
        const _this = this
        _this.shakeText()
        setInterval(() => {
            _this.shakeText()
        }, parseInt(this.loopTime))
        _this.switchDom = _this.$refs.switch
        _this.dragDom = _this.$refs.dragDom
        _this.switchWidth = _this.switchDom.offsetWidth
        //绑定事件
        const eventArr = ['touchstart', 'touchmove', 'touchend']
        eventArr.forEach(function (item, index) {
            _this.dragDom.addEventListener(item, _this.touchEvent)
        })
    },
    props: {
        tip: {
            type: String,
            default: '滑动来进入'
        },
        loopTime: {
            type: String,
            default: 3000,
        },
        speed: {
            type: String,
            default: 100,
        },
        effectTime: {
            type: String,
            default: 1000,
        },
        onDragPass: {
            type: Function,
        }
    },
    methods: {
        shakeText () {
            const _this = this
            for (let i = 0; i < _this.total; i++) {
                setTimeout(() => {
                    Vue.set(_this.shows, i, { show: !_this.shows[i].show })
                    const k = i
                    setTimeout(() => {
                        Vue.set(_this.shows, k, { show: !_this.shows[k].show })
                    }, parseInt(_this.effectTime))
                }, parseInt(_this.speed) * i)
            }
        },
        touchEvent (ev) {
            const _this = this
            const event = ev || window.event
            let dragX = '';
            switch (event.type) {
                case "touchstart":
                    _this.isDragging = true
                    _this.touchX = _this.touchX || event.touches[0].clientX
                    _this.clickX = event.touches[0].clientX - _this.switchDom.offsetLeft
                    removeClass(_this.dragDom, 'ani-ease-move')
                    break
                case "touchend":
                    _this.isDragging = false
                    if (_this.dragPercent < 100) {
                        this.$refs.cover.style.width = '0%'
                        this.dragDom.style.left = '0%';
                        addClass(this.dragDom, 'ani-ease-move')
                    }
                    break
                case "touchmove":
                    event.preventDefault()

                    dragX = (event.changedTouches[0].clientX - _this.switchDom.offsetLeft - _this.clickX)
                    dragX = dragX >= (_this.switchWidth - _this.dragDom.offsetWidth) ? (_this.switchWidth - _this.dragDom.offsetWidth) : (dragX < 0 ? 0 : dragX)
                    _this.dragDom.style.left = dragX + 'px'

                    _this.dragPercent = (((event.changedTouches[0].clientX - _this.switchDom.offsetLeft - _this.clickX + _this.dragDom.offsetWidth) / _this.switchWidth) * 100)
                    _this.dragPercent = _this.dragPercent >= 99.99 ? 100 : (_this.dragPercent < 0 ? 0 : _this.dragPercent)
                    _this.$refs.cover.style.width = _this.dragPercent + '%'
                    break
            }
        },
    },
    watch: {
        dragPercent (newVal, oldVal) {
            try {
                if (newVal > 99.9) {
                    this.onDragPass()
                }
            } catch (e) { }
        }
    }
}
</script>
<style scoped>
.ani-ease-move {
    transition: left 0.3s ease-out;
}
.big-switch {
    position: relative;
    display: block;
    width: 100%;
    max-width: 18rem;
    height: 4rem;
    border-radius: 4rem;
    margin: 0 auto;
    overflow: hidden;
}
.big-switch .tel-icon {
    width: 4rem;
    height: 4rem;
    position: absolute;
    left: 0;
    top: 0;
    z-index: 10;
    background: #fff;
    border-radius: 100%;
}
.big-switch .tel-icon .svg-icon {
    padding: 0.6rem;
    width: 2.8rem;
    height: 2.8rem;
    color: rgb(115, 200, 0);
    overflow: hidden;
    vertical-align: middle;
}
.big-switch .cover {
    position: absolute;
    z-index: 6;
    top: 0;
    width: 0%;
    height: 4rem;
    border-radius: 4rem;
    background-color: rgb(115, 200, 0);
}
.big-switch .txt {
    position: absolute;
    z-index: 5;
    width: calc(100% - 3.6rem);
    overflow: hidden;
    left: 3.6rem;
    text-align: center;
    font-size: 1.2rem;
    line-height: 4rem;
    user-select: none;
}
.big-switch .txt .ani {
    opacity: 0;
}
.big-switch .cylinderbg {
    position: absolute;
    z-index: 0;
    top: 0;
    width: 100%;
    height: 4rem;
    border-radius: 4rem;
    background-color: #fff;
    opacity: 0.6;
}
</style>
