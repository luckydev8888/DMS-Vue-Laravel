<template>
  <CSidebar
    fixed
    :minimize="minimize"
    :show="show"
    @update:show="(value) => $store.commit('set', ['sidebarShow', value])"
  >
    <CRenderFunction :key="RFKey" flat :content-to-render="$options.nav" />
    <CSidebarMinimizer
      class="d-md-down-none"
      @click.native="$store.commit('set', ['sidebarMinimize', !minimize])"
    />
  </CSidebar>
</template>

<script>
import nav from "./_nav";
import services from "../services/factory";

export default {
  name: "TheSidebar",
  nav,
  created() {
    this.init();
  },
  computed: {
    show() {
      return this.$store.state.sidebarShow;
    },
    minimize() {
      return this.$store.state.sidebarMinimize;
    },
    dashboard() {
      return {
        _name: 'CSidebarNavItem',
        name: 'Home page',
        to: '/dashboard',
        icon: 'cil-home',
      }
    },
  },
  methods: {
    async init() {
      const books = await this.fetchDocument();
      const system = await this.fetchSystem();
      const operating = await this.fetchOperating();
      this.$options.nav[0]._children = [this.dashboard , ...books, ...operating, ...system];
      this.RFKey += 1;
    },

    async fetchDocument() {
      return await services.book.all().then(response => {
        return [
          {
            _name: "CSidebarNavTitle",
            _children: ["Documents"]
          },
          ...response.data.map(item => {
            return {
              _name: "CSidebarNavItem",
              name: item.name,
              to: `/books/${item.id}/documents`,
              icon: "cil-notes"
            };
          })
        ];
      });
    },

    async fetchSystem() {
      return await services.auth.namePermissions().then(permissions => {
        return this.system.filter(item => {
          return !!item.permission
            ? permissions.includes(item.permission)
            : true;
        });
      });
    },

    async fetchOperating() {
      return await services.auth.namePermissions().then(permissions => {
        return this.operating.filter(item => {
          return !!item.permission
            ? permissions.includes(item.permission)
            : true;
        });
      });
    }
  },
  data() {
    return {
      RFKey: 0, // key for rerender sidebar
      operating: [
        {
          _name: "CSidebarNavTitle",
          _children: ["Permission"]
        },
        {
          _name: "CSidebarNavItem",
          name: "Statistical",
          to: "/statistic",
          icon: "cil-notes",
          permission: "Статистическая"
        }
      ],
      system: [
        {
          _name: "CSidebarNavTitle",
          _children: ["Система"]
        },
        {
          _name: "CSidebarNavItem",
          name: "User",
          to: "/users",
          icon: "cil-people",
          permission: "Разрешении пользователь"
        },
        {
          _name: "CSidebarNavItem",
          name: "Job title",
          to: "/titles",
          icon: "cil-contact",
          permission: "Разрешении должность"
        },
        {
          _name: "CSidebarNavItem",
          name: "Subdivision",
          to: "/departments",
          icon: "cil-lan",
          permission: "Разрешении подразделение"
        },
        {
          _name: "CSidebarNavItem",
          name: "Signed",
          to: "/signers",
          icon: "cil-touch-app",
          permission: "Разрешении подписал"
        },
        {
          _name: "CSidebarNavItem",
          name: "Place of issue",
          to: "/publishers",
          icon: "cil-institution",
          permission: "Разрешении место выдачи"
        },
        {
          _name: "CSidebarNavItem",
          name: "Document type",
          to: "/document-types",
          icon: "cil-text-size",
          permission: "Разрешении вид документа"
        },
        {
          _name: "CSidebarNavItem",
          name: "Documents",
          to: "/books",
          icon: "cil-book",
          permission: "Разрешении книги"
        },
        {
          _name: "CSidebarNavItem",
          name: "Group",
          to: "/roles",
          icon: "cil-address-book",
          permission: "Разрешении группа"
        },
        {
          _name: "CSidebarNavItem",
          name: "Permission",
          to: "/permissions",
          icon: "cil-lock-locked",
          permission: "Разрешении разрешение"
        }
      ]
    };
  }
};
</script>
