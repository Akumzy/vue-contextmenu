<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h2>Essential Links</h2>
    <p>right-click on any link</p>
    <ul>
      <li><a href="https://vuejs.org" target="_blank"> Core Docs </a></li>
      <li><a href="https://forum.vuejs.org" target="_blank"> Forum </a></li>
      <li>
        <a href="https://chat.vuejs.org" target="_blank"> Community Chat </a>
      </li>
      <li><a href="https://twitter.com/vuejs" target="_blank"> Twitter </a></li>
      <br />
      <li>
        <a href="http://vuejs-templates.github.io/webpack/" target="_blank">
          Docs for This Template
        </a>
      </li>
    </ul>
    <h2>Ecosystem</h2>
    <ul>
      <li>
        <a href="http://router.vuejs.org/" target="_blank"> vue-router </a>
      </li>
      <li><a href="http://vuex.vuejs.org/" target="_blank"> vuex </a></li>
      <li>
        <a href="http://vue-loader.vuejs.org/" target="_blank"> vue-loader </a>
      </li>
      <li>
        <a href="https://github.com/vuejs/awesome-vue" target="_blank">
          awesome-vue
        </a>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      msg: "How to create a custom context-menu"
    };
  },
  methods: {
    openLink(target) {
      console.log(target);
    },
    copyLink(target) {},
    linksHandler() {
      // let's handler all contextmenu event on all archor tags

      let links = this.$el.querySelectorAll("a");
      links.forEach(element => {
        element.addEventListener(
          "contextmenu",
          event => {
            const ctxMenuData = [
              {
                title: "Open link",
                handler: this.openLink.bind(this, element)
              },
              {
                type: "divider"
              },
              {
                title: "Copy link",
                handler: this.copyLink.bind(this, element)
              }
            ];
            this.$root.$emit("contextmenu", { event, ctxMenuData });
          },
          false
        );
      });
    }
  },
  mounted() {
    this.linksHandler();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
