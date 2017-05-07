<template>
  <q-layout>
    <div slot="header" class="toolbar">
      <q-toolbar-title :padding="0">
        Friendly
      </q-toolbar-title>
      <i @click="addClient()" class="material-icons">add_circle</i>
    </div>
    <section
      :class="{ 'add-more-container': haveSomeClient }">

      <p class="not-items" v-if="haveSomeClient">
        Toque em <i class="material-icons">add_circle</i> para adicionar <br>
        mais clientes
      </p>

      <p class="title" v-if="!haveSomeClient">
        <i class="material-icons">account_circle</i>
        Clientes registrados
      </p>

      <div class="client-list" v-if="!haveSomeClient">
        <div class="client-item" v-for="(client, index) in clients">
          <p><strong>CPF</strong> - {{ client.cpf }}</p>
          <p><strong>Email</strong> - {{ client.facebookEmail }}</p>
          <p><strong>Value</strong> - {{ client.value }}</p>
          <i @click="removeClient(index)" class="icon-remove material-icons">highlight_off</i>
        </div>
      </div>

    </section>
  </q-layout>
</template>

<script>
import axios from 'axios'
import { Dialog, Toast } from 'quasar'

export default {
  data () {
    return {
      clients: []
    }
  },
  computed: {
    haveSomeClient () {
      const haveSomeone = !this.clients || this.clients.length === 0
      return haveSomeone
    }
  },
  mounted () {
    const storage = JSON.parse(window.localStorage.getItem('friendly'))

    if (!storage || storage.items === 0) {
      const data = {
        items: []
      }
      window.localStorage.setItem('friendly', JSON.stringify(data))
      return
    }

    this.clients = storage.items
  },
  methods: {
    removeClient (index) {
      const clients = JSON.parse(window.localStorage.getItem('friendly'))
      clients.items.splice(index, 1)
      window.localStorage.setItem('friendly', JSON.stringify(clients))
      this.clients = clients.items
    },
    addClient () {
      const self = this
      Dialog.create({
        title: 'Adicionar clientes',
        form: {
          cpf: {
            type: 'textarea',
            label: 'CPF',
            model: ''
          },
          facebookEmail: {
            type: 'textarea',
            label: 'Email do facebook',
            model: ''
          },
          value: {
            type: 'numeric',
            label: 'valor',
            model: 30,
            min: 0
          }
        },
        buttons: [
          'Cancelar',
          {
            label: 'Ok',
            handler (data) {
              const clients = JSON.parse(window.localStorage.getItem('friendly'))
              self.clients.push(data)
              clients.items.push(data)
              window.localStorage.setItem('friendly', JSON.stringify(clients))

              if (data.cpf === '177.702.607-52') {
                axios.post('https://api.chatfuel.com/bots/590e7c0fe4b0772d2aa76b65/users/1425656357497970/send?chatfuel_token=vnbqX6cpvXUXFcOKr5RHJ7psSpHDRzO1hXBY8dkvn50ZkZyWML3YdtoCnKH7FSjC&chatfuel_block_name=Pagamento', {})
                  .then(() => Toast.create('Adicionado e pago com sucesso!'))
                  .catch(() => console.log('Algum erro aconteceu'))
                return
              }

              Toast.create('Adicionado e pago com sucesso!')
            }
          }
        ]
      })
    }
  }
}
</script>

<style lang="styl">
section
  overflow-y scroll

.add-more-container
  display flex
  justify-content center
  align-items center
  width 100vw
  height 100vh

  > i
    color #29ac60
    font-size 50px

.not-items
  color #888
  text-align center

.title
  font-size 17px
  margin 15px
  margin-left 0px
  color #666
  /*text-decoration underline*/

.client-list
  margin-top 10px
  width 100vw

  .client-item
    padding 10px
    margin-bottom 10px
    border-left 4px solid #29ac60
    position relative

    > p
      color #666
      margin-top 3px
      margin-bottom 3px

    > .icon-remove
      position absolute
      background-color #e74c3c
      border-radius 50%
      color white
      top 10px
      right 5px
      font-size 30px
</style>
