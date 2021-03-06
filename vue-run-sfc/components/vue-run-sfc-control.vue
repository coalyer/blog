<template>
  <!-- 底部控制区 -->
  <!-- 参考 element-ui https://github.com/ElemeFE/element/blob/dev/examples/components/demo-block.vue -->
  <div
    :style="isExpanded ? style : null"
    ref="control"
    v-show="!isScreenfull"
    class="vue-run-sfc-control"
    @click="$emit('expanded')"
  >
    <transition name="arrow-slide">
      <i
        class="vue-run-sfc-control-icon"
        :class="{ hovering: hovering || isExpanded }"
      ></i>
    </transition>
    <transition name="text-slide">
      <span class="control-text" v-if="hovering">{{ controlText }}</span>
    </transition>

    <transition name="text-slide">
      <span class="power-by-text"
        >Power By
        <a href="https://github.com/dream2023/vue-run-sfc" target="_blank"
          >Vue-Run-SFC</a
        ></span
      >
    </transition>
  </div>
</template>

<script>
const { throttle } = require("throttle-debounce");

export default {
  name: "vue-run-sfc-control",
  props: {
    isScreenfull: {
      type: Boolean,
      default: false
    },
    isExpanded: {
      type: Boolean,
      default: false
    },
    hovering: {
      type: Boolean,
      default: false
    },
    isRow: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      style: {}
    };
  },
  computed: {
    // 控制区的文本
    controlText() {
      return this.isExpanded ? "关闭编辑器" : "在线编辑代码";
    }
  },
  methods: {
    scrollHandler() {
      const controlHeight = 44;
      const wrapper = this.$parent.$refs.wrapper;
      if (this.isExpanded && wrapper) {
        const { top, bottom, left, width } = wrapper.getBoundingClientRect();

        const isAffix =
          bottom > document.documentElement.clientHeight &&
          top + controlHeight <= document.documentElement.clientHeight;
        this.style = isAffix
          ? {
              left: `${left}px`,
              width: `${width}px`,
              bottom: "0px",
              position: "fixed",
              border: "1px solid var(--vue-run-sfc-border, #ebeef5)"
            }
          : {};
      }
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.scrollHandler();
      this.throttleScrollHandler = throttle(100, () => {
        this.scrollHandler();
      });
      window.addEventListener("scroll", this.throttleScrollHandler);
    });
  },
  beforeDestroy() {
    window.removeEventListener("scroll", this.throttleScrollHandler);
  }
};
</script>

<style>
/* 控制器样式 */
.vue-run-sfc-control {
  border: 1px solid var(--vue-run-sfc-border, #ebeef5);
  height: 44px;
  box-sizing: border-box;
  background-color: #fff;
  border-bottom-left-radius: 4px;
  border-bottom-right-radius: 4px;
  text-align: center;
  margin-top: -1px;
  color: #d3dce6;
  cursor: pointer;
  position: relative;
  z-index: 1;
}
.vue-run-sfc-control:hover {
  color: var(--vue-run-sfc-main, #409eff);
  background-color: #f9fafc;
}

.vue-run-sfc-control-icon {
  width: 0;
  height: 0;
  border-top: 6px solid #d3dce6;
  border-right: 6px solid transparent;
  border-left: 6px solid transparent;
  -webkit-transition: 0.3s;
  transition: 0.3s;
  position: relative;
  font-size: 0;
  display: inline-block;
  vertical-align: middle;
  bottom: -9px;
}

.vue-run-sfc-control-icon.hovering {
  transform: translateX(-40px) rotate(180deg);
}
.vue-run-sfc-control:hover .vue-run-sfc-control-icon {
  border-top: 6px solid var(--vue-run-sfc-main, #409eff);
}
.vue-run-sfc-control .control-text {
  position: absolute;
  transform: translateX(-30px);
  font-size: 14px;
  line-height: 44px;
  transition: 0.3s;
  display: inline-block;
}
.vue-run-sfc-control .power-by-text {
  position: absolute;
  right: 10px;
  top: 10px;
  font-size: 14px;
  opacity: 0.8;
}
.vue-run-sfc-control .power-by-text > a {
  color: inherit;
}

.vue-run-sfc-control .text-slide-enter,
.vue-run-sfc-control .text-slide-leave-active {
  opacity: 0;
  transform: translateX(10px);
}
</style>
