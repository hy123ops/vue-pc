<style type="less" scoped>
.layout {
  border: 1px solid #d7dde4;
  background: #f5f7f9;
  position: relative;
  border-radius: 4px;
  overflow: hidden;
}
.layout-header-bar {
  background: #fff;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.1);
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.layout-logo-left {
  width: 90%;
  height: 30px;
  background: #5b6270;
  border-radius: 3px;
  margin: 15px auto;
}
.menu-icon {
  transition: all 0.3s;
}
.rotate-icon {
  transform: rotate(-90deg);
}
.menu-item span {
  display: inline-block;
  overflow: hidden;
  width: 69px;
  text-overflow: ellipsis;
  white-space: nowrap;
  vertical-align: bottom;
  transition: width 0.2s ease 0.2s;
}
.menu-item i {
  transform: translateX(0px);
  transition: font-size 0.2s ease, transform 0.2s ease;
  vertical-align: middle;
  font-size: 16px;
}
.collapsed-menu span {
  width: 0px;
  transition: width 0.2s ease;
}
.collapsed-menu i {
  transform: translateX(5px);
  transition: font-size 0.2s ease 0.2s, transform 0.2s ease 0.2s;
  align-items: center !important;
  font-size: 22px;
}
.bread-crumb {
  width: 300px;
  display: flex;
  justify-content: flex-end;
  padding-right: 20px;
}
</style>
<template>
  <div class="layout">
    <Layout>
      <Header>
        <div>00</div>
      </Header>
      <Layout>
        <Sider ref="side1" hide-trigger collapsible :collapsed-width="78" v-model="isCollapsed">
          <Menu :active-name="currentName" theme="dark" width="auto" :class="menuitemClasses">
            <MenuItem name="1-1" to="/home">
              <Icon type="ios-navigate"></Icon>
              <span>首页</span>
            </MenuItem>
            <MenuItem name="1-2" to="/position">
              <Icon type="ios-search"></Icon>
              <span>电影管理</span>
            </MenuItem>
          </Menu>
        </Sider>
        <Layout>
          <Header :style="{padding: 0}" class="layout-header-bar">
            <Icon
              @click.native="collapsedSider"
              :class="rotateIcon"
              :style="{margin: '0 20px'}"
              type="md-menu"
              size="24"
            ></Icon>
            <Breadcrumb class="bread-crumb">
              <BreadcrumbItem to="/home">
                <Icon type="ios-home-outline"></Icon>首页
              </BreadcrumbItem>
              <BreadcrumbItem to="/position">
                <Icon type="logo-buffer"></Icon>电影管理
              </BreadcrumbItem>
              <!-- <BreadcrumbItem>Breadcrumb</BreadcrumbItem> -->
            </Breadcrumb>
          </Header>
          <Content :style="{margin: '20px', background: '#fff', minHeight: '260px'}">
            <router-view @onRouteChange="handleRouteChange"></router-view>
          </Content>
        </Layout>
      </Layout>
      <Footer></Footer>
    </Layout>
  </div>
</template>
<script>
import {
  Layout,
  Sider,
  Menu,
  MenuItem,
  Icon,
  Header,
  Content,
  Footer,
  Breadcrumb,
  BreadcrumbItem
} from "view-design";
export default {
  data() {
    return {
      isCollapsed: false,
      currentName: "1-1"
    };
  },
  computed: {
    rotateIcon() {
      return ["menu-icon", this.isCollapsed ? "rotate-icon" : ""];
    },
    menuitemClasses() {
      return ["menu-item", this.isCollapsed ? "collapsed-menu" : ""];
    }
  },
  methods: {
    collapsedSider() {
      this.$refs.side1.toggleCollapse();
    },

    handleRouteChange(to) {
      let map = {
        position: "1-2",
        home: "1-1"
      };
      this.currentName = map[to.name];
    }
  },
  components: {
    Layout,
    Sider,
    Menu,
    MenuItem,
    Icon,
    Header,
    Content,
    Footer,
    Breadcrumb,
    BreadcrumbItem
  },

  beforeRouteEnter(to, from, next) {
    console.log(0);
    let map = {
      position: "1-2",
      home: "1-1"
    };
    console.log(this);
    this.currentName = map[to.name];
  }
};
</script>