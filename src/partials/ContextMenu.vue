<template>
  <div
    class="ctx-menu"
    :style="style"
    :hidden="!ctxMenuData"
    v-click-outside="resetCtx"
  >
    <template v-if="ctxMenuData">
      <template v-for="(item, index) in ctxMenuData">
        <div
          v-if="item.type !== 'divider'"
          :key="index"
          :ref="'ctx_' + index"
          :id="'ctx_' + index"
        >
          <span v-text="item.title" class="ctx-menu-option"></span>
        </div>
        <div v-else :key="index" class="ctx-menu-divider"><span></span></div>
      </template>
    </template>
  </div>
</template>

<script>
export default {
  name: "ContextMenu",
  data: () => ({
    style: null,
    ctxMenuData: null,
    // the options schema
    // [
    //   {
    //     title: string,
    //     type: string, // default is divider
    //     handler: function
    //   }
    // ]
    ctxMenuRect: null
  }),
  methods: {
    resetCtx() {
      this.ctxMenuData = null;
      this.ctxMenuRect = null;
    },
    onContextMenu(ev, ctxMenuData) {
      // prevent default behaviours
      ev.preventDefault();
      ev.stopPropagation();
      this.ctxMenuData = ctxMenuData;
      this.ctxMenuRect = {
        x: ev.x,
        y: ev.y
      };
      // populate the option
      this.onData();
      // then reevaluate and set context-menu position
      this.reevaluatePosition();
    },
    async reevaluatePosition() {
      if (this.ctxMenuRect) {
        // using $nextTick to daley and make sure that the context-menu
        // options are fully rendered which will help us
        // to get the accurate height
        await this.$nextTick();
        await this.$nextTick();

        let { x, y } = this.ctxMenuRect;
        // get the window current inner height and width
        let { innerHeight, innerWidth } = window;
        // get the component height and width through element.getClientRects
        let { height, width } = this.$el.getClientRects()[0];
        // then subtract window inner height and width with
        // context-menu event source points (x, y)
        let dY = innerHeight - y;
        let dX = innerWidth - x;
        // check if the context-menu height is not
        // longer than the available
        if (dY < height) {
          y = y - height;
        }
        if (dX < width) {
          x = x - width;
        }
        this.style = { left: x + "px", top: y + "px" };
      }
    },
    async onData() {
      if (Array.isArray(this.ctxMenuData) && this.ctxMenuData.length) {
        await this.$nextTick();
        this.ctxMenuData.forEach((item, index) => {
          if (item.type !== "divider" && typeof item.handler === "function") {
            let refs = this.$refs["ctx_" + index];
            if (Array.isArray(refs)) {
              let el = refs[0];
              el.addEventListener(
                "click",
                () => {
                  item.handler();
                  this.resetCtx();
                },
                false
              );
            }
          }
        });
      }
    }
  },
  mounted() {
    this.$root.$on("contextmenu", data => {
      if (data === null) this.resetCtx();
      else this.onContextMenu(data.event, data.ctxMenuData);
    });
  },
  beforeDestroy() {
    this.$root.$off("contextmenu", () => {});
  }
};
</script>

<style scoped>
.ctx-menu {
  min-width: 150px;
  height: fit-content;
  padding: 10px 0;
  position: fixed;
  background-color: #fff;
  box-shadow: 0 2px 5px 0 rgba(0, 0, 0, 0.26), 0 2px 10px 0 rgba(0, 0, 0, 0.16);
  border-radius: 3px;
  z-index: 1111111;
}
.ctx-menu-option {
  display: flex;
  align-items: center;
  height: 35px;
  padding: 0 10px;
  cursor: pointer;
}
.ctx-menu-option:hover {
  background-color: #f3f9ff;
}
.ctx-menu-divider {
  height: 16px;
  display: flex;
  justify-content: center;
  align-items: center;
}
.ctx-menu-divider > span {
  width: 50px;
  height: 1px;
  background-color: #dcdfe6;
}
</style>
