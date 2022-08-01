<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      app
      clipped
      dark
      :mini-variant="miniVariant"
    >
      <v-list shaped class="menu-list mt-5">

        <v-list-group
          v-for="(item, i) in modules"
          :key="i"
          active-class="blue--text"
          mandatory
          :ripple="{ class: 'blue--text' }"
          :prepend-icon="miniVariant ? item.icon : ''"
        >
          <template v-slot:activator>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </template>
          <v-list-item
            v-for="(subitem, j) in item.submodules"
            :key="j + 'sb'"
            :to="subitem.to"
            router
            exact
          >
            <v-list-item-action>
              <v-icon size="20">{{ subitem.icon }}</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title
                active-class="white--text"
                v-text="subitem.title"
              />
            </v-list-item-content>
          </v-list-item>
        </v-list-group>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar :clipped-left="true" fixed app color="#005b94" dense>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" class="menu-icon" />
      <v-btn icon @click.stop="miniVariant = !miniVariant">
        <v-icon>mdi-{{ `chevron-${miniVariant ? 'right' : 'left'}` }}</v-icon>
      </v-btn>
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer :absolute="!fixed" app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
// import Cookies from 'js-cookie'
export default {
  name: 'DefaultLayout',
  data() {
    return {
      clipped: true,
      drawer: false,
      fixed: true,
      modules: [
        {
          title: 'FACTURAS',
          icon: 'mdi-cog-outline',
          submodules: [
            {
              icon: 'mdi-form-select',
              title: 'Listado',
              to: '/invoices/list',
            },
          ],
        },
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js',
    }
  },
  methods: {
    setRoute(route) {
      // console.log(route);
      window.location = route
    },
    logout() {
      // Cookies.remove('wms_token')
      // window.location = '/login'
    },
  },
}
</script>
<style>
.menu-user {
  width: 250px;
  height: auto;
  padding: 20px 20px 10px 20px;
  background-color: white;
}
.menu-list .v-icon {
  color: #005b94 !important;
}
.menu-list .v-icon:hover {
  color: #005b94 !important;
}
.menu-list .v-icon:active {
  color: #f05c62 !important;
}
.menu-list-module:active {
  color: #f05c62 !important;
}
</style>
