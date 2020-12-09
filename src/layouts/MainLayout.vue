<template>
  <q-layout view="lHh Lpr lFf">
    <q-header class="bg-white">
      <q-toolbar class="justify-end text-black">
        <q-tabs
          v-model="tab"
          no-caps
          class="full-width row justify-center"
          active-color="primary"
          indicator-color="primary"
          narrow-indicator
        >
          <q-tab name="info" label="О приложении" />
          <q-tab name="userLoading" label="Загрузка пользователей" />
          <q-tab name="userList" label="Список пользователей" />
        </q-tabs>
        <div>
          <span class="text-weight-bold">Техподдержка</span>
        </div>
      </q-toolbar>

      <q-separator/>
    </q-header>
    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>

export default {
  name: 'MainLayout',
  data () {
    return {
      leftDrawerOpen: false,
      tab: 'userList'
    }
  }
}
</script>
