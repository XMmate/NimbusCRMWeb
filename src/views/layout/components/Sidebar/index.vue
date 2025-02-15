<template>
  <div class="sidebar-container">
    <div
      :style="{ 'padding-top': createButtonTitle != '' ? '40px' : '25px', 'background-color':variables.menuBg }"
      class="create-button-container">
      <el-popover
        v-if="createButtonTitle != ''"
        :offset="addOffset"
        :visible-arrow="false"
        :disabled="!$slots.add"
        placement="right"
        popper-class="no-padding-popover"
        trigger="hover">
        <slot name="add" />
        <div
          slot="reference"
          class="create-button"
          @click="quicklyCreate">
          <div
            v-show="!buttonCollapse"
            class="button-name">{{ createButtonTitle }}</div>
          <div
            v-show="!buttonCollapse"
            class="button-line" />
          <i
            :class="createButtonIcon"
            class="button-mark" />
        </div>
      </el-popover>
    </div>
    <el-scrollbar
      :style="{'border-right-color': variables.menuBg, 'padding-top': createButtonTitle != '' ? '90px' : '40px'}"
      wrap-class="scrollbar-wrapper">
      <el-menu
        :default-active="activeMenu"
        :collapse="collapse"
        :background-color="variables.menuBg"
        :text-color="variables.menuText"
        :active-text-color="variables.menuActiveText"
        :style="{ paddingBottom: paddingBottom}"
        mode="vertical"
        class="el-menu-vertical"
        @select="handleSelect">
        <sidebar-item
          v-for="(route, index) in items"
          :key="`${route.path}${index}`"
          :item="route"
          :collapse="collapse"
          :base-path="route.path"
          :active-menu="activeMenu" />
      </el-menu>
    </el-scrollbar>
    <slot name="bottom"/>
    <div
      :style="{ 'background-color':variables.menuBg }"
      class="sidebar-bottom">
      <slot name="sidebar-bottom"/>
      <div class="sidebar-bottom-content">
        <div v-if="!collapse" class="copyright">
          <img src="/favicon.ico" width="20px" >
          <span>Power by liujiaming</span>
        </div>
        <img
          :style="{ 'right': buttonCollapse ? '3px' : '0' }"
          :class="{ 'is-close': collapse }"
          class="collapse-button"
          src="@/assets/img/collapse_white.png"
          alt=""
          @click="toggleSideBarClick">
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import SidebarItem from './SidebarItem'
import variables from './variables.scss'

export default {
  components: { SidebarItem },
  props: {
    items: {
      type: Array,
      default: () => {
        return []
      }
    },
    addOffset: {
      type: Number,
      default: 70
    },
    createButtonTitle: {
      type: String,
      default: ''
    },
    createButtonIcon: {
      type: String,
      default: 'el-icon-plus'
    },
    // 防止菜单被底部布局遮住
    paddingBottom: {
      type: String,
      default: '48px'
    }
  },
  data() {
    return {
      buttonCollapse: false
    }
  },
  computed: {
    ...mapGetters(['collapse']),
    activeMenu() {
      const route = this.$route
      const { meta, path, params } = route

      let title = this.WKConfig.companyName
      if (meta.title) {
        title += ' - ' + meta.title
      } else if (params && params.title) {
        title += ' - ' + params.title
      }
      document.title = title
      // if set path, the sidebar will highlight the path you set
      if (meta.activeMenu) {
        return meta.activeMenu
      }
      return path
    },
    variables() {
      return variables
    }
  },
  watch: {
    collapse: function(val) {
      if (val) {
        this.buttonCollapse = val
      } else {
        setTimeout(() => {
          this.buttonCollapse = val
        }, 300)
      }
    }
  },
  mounted() {
    this.buttonCollapse = this.collapse
  },
  methods: {
    toggleSideBarClick() {
      this.$store.commit('SET_COLLAPSE', !this.collapse)
    },

    // 快速创建
    quicklyCreate() {
      this.$emit('quicklyCreate')
    },

    handleSelect(key, keyPath) {
      this.$emit('select', key, keyPath)
    }
  }
}
</script>
<style lang="scss" scoped>
@import './variables.scss';

.sidebar-container {
  transition: width 0.28s;
  width: auto;
  height: 100%;
  position: relative;
  background-color: $menuBg;
  overflow: auto;
  flex-shrink: 0;

  .scrollbar-wrapper {
    overflow-x: hidden !important;
  }

  .el-scrollbar {
    height: 100%;
  }

  a {
    display: inline-block;
    width: 100%;
    overflow: hidden;
  }
}

.el-menu-vertical:not(.el-menu--collapse) {
  width: 200px;
  min-height: 400px;
}

.el-menu-vertical {
  height: 100%;
  overflow-y: auto;
  overflow-y: overlay;
  overflow-x: hidden;
  padding-bottom: 48px;
  border-right-color: $menuBg;
}

.el-menu-vertical.el-menu--collapse {
  /deep/ .el-submenu__icon-arrow {
    display: none;
  }

  /deep/ .el-submenu__title {
    span {
      display: none;
    }
  }

  /deep/ .menu-item-content {
    text-overflow: clip;
  }
}

// 创建

.create-button-container {
  padding: 15px 14px;
  color: white;
  font-size: 14px;
  cursor: pointer;
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  z-index: 2;


  .create-button {
    display: flex;
    align-items: center;
    justify-content: center;
    box-sizing: border-box;
    padding: 0 15px;
    height: 36px;
    border-radius: $xr-border-radius-base;
    background-color: rgba($color: #fff, $alpha: 0.1);
    color: #999;

    .button-name {
      flex: 1;
    }

    .button-line {
      height: 10px;
      background-color: white;
      width: 1px;
      margin: 0 20px 0 10px;
      opacity: 0.3;
    }

    .button-mark {
      width: 12px;
    }
  }

  .create-button:hover {
    color: white !important;
    background-color: $xr-color-primary !important;
  }
}

// 底部按钮
.sidebar-bottom {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  // height: 48px;

  &-content {
    position: relative;
    height: 48px;
  }

  .copyright {
    color: white;
    font-size: 12px;
    height: 100%;
    padding-left: 20px;
    line-height: 3.5;
    img,span {
      vertical-align: middle;
    }
  }
}

.collapse-button {
  position: absolute;
  top: 0;
  padding: 18px 20px;
  cursor: pointer;
}

.collapse-button.is-close {
  transform: rotate(180deg);
}
</style>

