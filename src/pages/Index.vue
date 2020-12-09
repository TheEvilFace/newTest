<template>
  <q-page class="q-pt-xl">
    <!--Верхний блок-->
    <div class="full-width row justify-center q-mb-md">
      <p class="text-h6 text-weight-bold">Список экспертов по оценке и руководителей</p>
    </div>
    <!--Фильтры-->
    <div class="q-px-xl row justify-between items-center q-my-md">
      <div class="col-2">
        <p class="text-primary text-weight-bold q-mb-xs">ID</p>
        <q-select class="shadow-6 q-px-sm rounded-select"
                  emit-value
                  map-options
                  dense
                  borderless
                  v-model="selectFilter.id"
                  :options="getArrayByOptionName('id')"/>
      </div>
      <div class="col-2">
        <p class="text-primary text-weight-bold q-mb-xs">ФИО</p>
        <q-select class="shadow-6 q-px-sm rounded-select"
                  emit-value
                  map-options
                  dense
                  borderless
                  v-model="selectFilter.fio"
                  :options="getArrayByOptionName('fio')"/>
      </div>
      <div class="col-2">
        <p class="text-primary text-weight-bold q-mb-xs">Должность</p>
        <q-select class="shadow-6 q-px-sm rounded-select"
                  emit-value
                  map-options
                  dense
                  borderless
                  v-model="selectFilter.rank"
                  :options="getArrayByOptionName('rank')"/>
      </div>
      <div class="col-2">
        <p class="text-primary text-weight-bold q-mb-xs">Почта(логин)</p>
        <q-select class="shadow-6 q-px-sm rounded-select"
                  emit-value
                  map-options
                  dense
                  borderless
                  v-model="selectFilter.login"
                  :options="getArrayByOptionName('login')"/>
      </div>
      <q-btn color="primary" class="rounded-select q-mt-lg col-2" padding="sm" no-caps><span>Применить фильтры</span>
      </q-btn>
    </div>
    <!--Таблица пользователей-->
    <div class="q-mt-xl q-px-sm">
      <q-table
        selection="single"
        :selected.sync="selected"
        class="text-primary"
        :data="userList"
        :columns="сolumns"
        hide-pagination
        :filter-method="customFilterMethod"
        :filter="selectFilter"
        :pagination="pagination"
        :separator="'cell'"
      >
        <template v-slot:body-cell-created_at="props">
          <q-td key="created_at" :props="props">
            <span>{{ props.row.created_at | moment("MM.DD.YYYY") }}</span>
          </q-td>
        </template>
        <template v-slot:body-cell-selected="props">
          <q-td key="selected" :props="props">
            <span>{{ props.row.created_at | moment("MM.DD.YYYY") }}</span>
          </q-td>
        </template>

        <template v-slot:body-cell-password="props">
          <q-td key="password" :props="props">
            <span>********</span>
          </q-td>
        </template>

        <template v-slot:body-cell-phone="props">
          <q-td key="phone" :props="props">
            <span>{{ phoneMask(props.row.phone) }}</span>
          </q-td>
        </template>
      </q-table>
    </div>
    <!--Пагинация и добавление-->
    <div class="row justify-center q-mt-md q-px-sm">
      <div class="col-4">

      </div>
      <div class="col-4 flex justify-center">
        <q-pagination
          v-model="pagination.page"
          :max="pagesNumber"
          size="sm"
          :direction-links="true"
        />
      </div>
      <div class="col-4 flex justify-end">
        <q-btn @click="newUser = !newUser" class="bg-primary text-white rounded-select" no-caps
               label="Добавить пользователя"></q-btn>
      </div>

    </div>
    <!--Форма добавления пользователя-->
    <q-dialog v-model="newUser">
      <q-card class="q-px-lg">
        <p class="text-h6 text-weight-bold text-primary q-mt-md">Добавление данных о экспертах по оценке и
          руководителей</p>
        <q-form @submit="addNewUser()" class="row justify-center">
          <div class="column">
            <div class="q-mb-md">
              <p class="text-primary text-weight-bold q-mb-none">Дата</p>
              <q-input
                v-model="formAdd.created_at"
                dense
                borderless
                lazy-rules
                type="date"
                :rules="[ val => val && val.length > 0 || 'Укажите дату']"
                class="shadow-6 q-px-sm q-py-none rounded-select"/>
            </div>

            <div class="q-my-md">
              <p class="text-primary text-weight-bold q-mb-none">ФИО</p>
              <q-input
                v-model="formAdd.fio"
                dense
                placeholder="Введите ФИО"
                borderless lazy-rules
                type="text"
                :rules="[ val => val && val.length > 0 || 'Укажите ФИО']"
                class="shadow-6 q-px-sm q-py-none rounded-select"/>
            </div>

            <div class="q-my-md">
              <p class="text-primary text-weight-bold q-mb-none">Должность</p>
              <q-input
                v-model="formAdd.rank"
                dense
                placeholder="Укажите должность"
                borderless
                lazy-rules
                type="text"
                :rules="[ val => val && val.length > 0 || 'Укажите должность']"
                class="shadow-6 q-px-sm q-py-none rounded-select"/>
            </div>

            <div class="q-my-md">
              <p class="text-primary text-weight-bold q-mb-none">Введите почту(логин)</p>
              <q-input
                v-model="formAdd.login"
                dense
                placeholder="Введите почту(логин)"
                borderless
                type="text"
                lazy-rules
                :rules="[ val => val && val.length > 0 || 'Введите почту(логин)']"
                class="shadow-6 q-px-sm q-py-none rounded-select"/>
            </div>

            <div class="q-my-md">
              <p class="text-primary text-weight-bold q-mb-none">Пароль</p>
              <div class="row items-center relative-position">
                <q-input
                  v-model="formAdd.password"
                  dense
                  placeholder="Введите пароль"
                  borderless
                  type="password"
                  lazy-rules
                  :rules="[ val => val && val.length > 0 || 'Введите пароль']"
                  class="shadow-6 q-px-sm q-py-none rounded-select col-12"/>
                <q-icon name="local_post_office" class="absolute-right letter-in-dialog" color="primary" size="md"></q-icon>
              </div>

            </div>

            <div class="q-my-md">
              <p class="text-primary text-weight-bold q-mb-none">Телефон</p>
              <q-input
                v-model="formAdd.phone"
                dense
                placeholder="Введите телефон"
                borderless
                type="phone"
                lazy-rules
                :rules="[ val => val && val.length > 0 || 'Введите телефон']"
                class="shadow-6 q-px-sm q-py-none rounded-select"/>
            </div>
            <q-btn type="submit" color="primary" class="q-mb-md big-space" label="Сохранить"></q-btn>
          </div>
        </q-form>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script>
import styleIndex from '../css/indexStyle.css'

export default {
  name: 'PageIndex',
  computed: {
    pagesNumber() {
      return Math.ceil(this.userList.length / this.pagination.rowsPerPage)
    },
  },
  data() {
    return {
      pagination: {
        page: 1,
        rowsPerPage: 3
      },
      newUser: false,
      selectFilter: {
        id: null,
        fio: null,
        rank: null,
        login: null
      },
      filter:{
        id: '',
        fio: '',
        rank: '',
        login: ''
      },
      formAdd: {
        id: null,
        created_at: this.$moment().format('YYYY-MM-DD'),
        fio: '',
        rank: '',
        login: '',
        password: '',
        phone: ''
      },
      selected: [],
      сolumns: [
        {name: 'id', required: true, label: 'ID', align: 'center', field: 'id'},
        {name: 'created_at', align: 'center', label: 'Дата регистрации', field: 'created_at'},
        {name: 'fio', align: 'center', label: 'ФИО', field: 'fio'},
        {name: 'rank', align: 'center', label: 'Должность', field: 'rank'},
        {name: 'login', align: 'center', label: 'Почта(логин)', field: 'login'},
        {name: 'password', align: 'center', label: 'Пароль', field: 'password'},
        {name: 'phone', align: 'center', label: 'Телефон привязанный к менеджеру', field: 'phone'},
      ],
      userList: [
        {
          id: 1,
          created_at: new Date(),
          fio: 'Геринович Михаил Сергеевич',
          rank: 'Программист',
          login: 'proger',
          password: 'sadsad',
          phone: '+7 800-555-35-35'
        },
        {
          id: 2,
          created_at: new Date(),
          fio: 'Петров Иван Дмитриевич',
          rank: 'Консультант',
          login: 'konsultant',
          password: 'dsadsds',
          phone: '+7 994-555-35-35'
        },
      ]
    }
  },
  methods: {
    //Кто пихает фильтры на фронт? Работать будет только тем фильтром который идет раньше(можно прописать 256 условий, но это перебор)
    customFilterMethod () {
      if(this.selectFilter.id) {
       return this.userList.filter(row => row.id === this.selectFilter.id);
      }
      if(this.selectFilter.fio) {
        return this.userList.filter(row => row.fio === this.selectFilter.fio)
      }
      if(this.selectFilter.rank) {
        return this.userList.filter(row => row.rank === this.selectFilter.rank)
      }
      if(this.selectFilter.login) {
        return this.userList.filter(row => row.login === this.selectFilter.login)
      }
      return this.userList
  },
    addNewUser(){
      this.formAdd.id = this.userList.length + 1;
      this.userList.push(this.formAdd);
    },
    phoneMask(phone) {
      const regExp = new RegExp(/[0-9]/, 'g');
      return '' + phone.slice(0, 6) + phone.slice(6).replaceAll(regExp, 'x');
    },
    getArrayByOptionName(optionName) {
      let array;
      switch (optionName) {
        case 'id': {
          array = [{
            label: 'Выберите ID',
            value: null
          }];
          break;
        }
        case 'fio': {
          array = [{
            label: 'Выберите ФИО',
            value: null
          }];
          break;
        }
        case 'rank': {
          array = [{
            label: 'Выберите должность',
            value: null
          }];
          break;
        }
        case 'login': {
          array = [{
            label: 'Выберите Почту(логин)',
            value: null
          }];
          break;
        }
      }

      this.userList.forEach(key => {
        array.push(key[optionName]);
      });
      return array;
    }
  }
}
</script>
