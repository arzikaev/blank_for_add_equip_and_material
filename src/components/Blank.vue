<template>
  <v-container>
    <v-row class="ma-1 pa-1">
      <v-col>
        <template>
          <v-row class="ma-1 pa-1">
            <v-dialog
                v-model="dialog"
                fullscreen
                hide-overlay
                transition="dialog-bottom-transition"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                    color="orange"
                    dark
                    v-bind="attrs"
                    v-on="on"
                >
                  Добавить
                </v-btn>
              </template>
              <v-card>
                <v-toolbar
                    dark
                    color="primary"
                >
                  <v-btn
                      icon
                      dark
                      @click="dialog = false"
                  >
                    <v-icon>mdi-close</v-icon>
                  </v-btn>
                  <v-toolbar-title>Форма добавления техники</v-toolbar-title>
                  <v-spacer></v-spacer>
                  <v-toolbar-items>
                    <v-btn
                        dark
                        text
                        @click="saveRow"
                    >
                      Сохранить
                    </v-btn>
                  </v-toolbar-items>
                </v-toolbar>
                <v-card-text>
                  <v-text-field
                      class="ma-2 pa-2"
                      height="50%"
                      v-model="techTitle"
                      label="Наименование техники"
                      required
                  ></v-text-field>
                  <v-menu
                      ref="menu"
                      v-model="menu"
                      :close-on-content-click="false"
                      transition="scale-transition"
                      offset-y
                      min-width="auto"
                  >
                    <template v-slot:activator="{ on, attrs }">
                      <v-text-field
                          v-model="dates"
                          label="Дата работ"
                          prepend-icon="mdi-calendar"
                          readonly
                          v-bind="attrs"
                          v-on="on"
                      ></v-text-field>
                    </template>
                    <v-date-picker
                        v-model="dates"
                    >
                      <v-spacer></v-spacer>
                      <v-btn
                          text
                          color="primary"
                          @click="menu = false"
                      >
                        Отмена
                      </v-btn>
                      <v-btn
                          text
                          color="primary"
                          @click="$refs.menu.save(dates)"
                      >
                        Сохранить
                      </v-btn>
                    </v-date-picker>
                  </v-menu>
                  <v-text-field
                      class="ma-2 pa-2"
                      v-model="contragent"
                      label="Наименование организации"
                      required
                  ></v-text-field>
                  <v-text-field
                      class="ma-2 pa-2"
                      v-model="time"
                      label="Часы работы"
                      required
                  ></v-text-field>
                  <v-text-field
                      class="ma-2 pa-2"
                      v-model="hours"
                      type="number"
                      label="Количество часов"
                      required
                  ></v-text-field>
                  <v-textarea
                      class="ma-2 pa-2"
                      counter
                      label="Вид работ подробнее"
                      v-model="comments"
                      required
                  ></v-textarea>
                </v-card-text>
              </v-card>
            </v-dialog>
          </v-row>
        </template>
      </v-col>
    </v-row>
    <v-row class="ma-2 pa-2">
      <v-col>
        <template>
          <v-simple-table>
            <template v-slot:default>
              <thead>
              <tr class="gray">
                <th class="text-center">
                </th>
                <th class="text-center">
                  Наименование организации
                </th>
                <th class="text-center">
                  Наименование техники
                </th>
                <th class="text-center">
                  Дата работы
                </th>
                <th class="text-center">
                  Часы работы
                </th>
                <th class="text-center">
                  Количество часов
                </th>
                <th class="text-center">
                  Вид работ подробнее
                </th>
              </tr>
              </thead>
              <template v-if="loading === true">
                <v-progress-linear
                    :active="loading"
                    :indeterminate="loading"
                    absolute
                    top
                    color="light-green darken-4"
                    height="10"
                ></v-progress-linear>
              </template>
              <tbody>
              <template
                  v-for="item in itemsData"
              >
                <tr :key="item.id" class="gray">
                  <td>
                    <v-btn
                        icon
                        @click="deleteRow(item.id)"
                    >
                      <v-icon>
                        mdi-trash-can-outline
                      </v-icon>
                    </v-btn>
                  </td>
                  <td>{{ item.contragent }}</td>
                  <td>{{ item.technics }}</td>
                  <td>{{ item.date }}</td>
                  <td>{{ item.time }}</td>
                  <td>{{ item.hours }}</td>
                  <td>{{ item.comments }}</td>
                </tr>
              </template>
              </tbody>
            </template>
          </v-simple-table>
        </template>
      </v-col>
    </v-row>
    <v-row class="ma-1 pa-1">
      <v-col>
        <v-btn color="success" @click="save">
          Занести в отчет
        </v-btn>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Blank',

  data: () => ({
    loading: false,
    dates: '',
    normalDate:'',
    menu: false,
    itemsData: [],
    dialog: false,
    techTitle: '',
    contragent: '',
    time: '',
    hours: '',
    comments: '',
    url: 'https://citystroi.bitrix24.ru/rest/73/gjdt5g3y10jxw3r9/'
  }),
  methods: {
    deleteRow(id){
      const indexItem = this.itemsData.map(e => e.id).indexOf(id);
      console.log({indexItem})
      this.itemsData.splice(indexItem, 1)
    },
    async normalizeDate() {
      try {
        const date = this.dates.split('-')
        console.log(date)
         this.normalDate = date[2] + '.' + date[1] + '.' + date[0] + ' 00:00:00'
      } catch (e) {
        console.log(e)
      }
    },
    saveRow() {
      this.normalizeDate()
      const row = {
        id: this.itemsData.length + 1,
        contragent: this.contragent,
        technics: this.techTitle,
        date: this.normalDate,
        time: this.time,
        hours: this.hours,
        comments: this.comments
      }
      console.log({row})
      this.itemsData.push(row)
      //console.log(this.itemsData)
      this.dialog = false
      this.comments = ''
      this.hours = ''
      this.dates = ''
      this.normalDate = ''
      this.techTitle = ''
      this.contragent = ''
      this.time = ''
    },
    async save(){
      try {
        this.loading = true
        console.log(this.itemsData)
        for(const item of this.itemsData){
          const params = {
            'IBLOCK_TYPE_ID': 'lists',
            'IBLOCK_ID': '101',
            'ELEMENT_CODE': `item${item.technics}&${item.date}`,
            'FIELDS': {
              'NAME': item.technics,
              'PROPERTY_1057': item.date,
              'PROPERTY_1059': item.contragent,
              'PROPERTY_1061': item.time,
              'PROPERTY_1063': item.hours,
              'PREVIEW_TEXT': item.comments,
            }
          }
          const method = 'lists.element.add'
          const rowAdd = await axios.post(this.url + method, params)
          console.log({rowAdd})
        }
        this.itemsData = []
        this.loading = false
      }catch (e) {
        console.log(e)
      }
    }
  }
}
</script>
