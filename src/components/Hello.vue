<template>
  <q-layout
    ref="layout"
    view="lHh Lpr fff"
    :left-class="{'bg-grey-2': true}"
  >



        <s-take-pictured
          buttonsStyle="flat"
          buttonColor="primary"
          @pictured="img"
          height="50vh"
          width="100%"
        >

        </s-take-pictured>




  </q-layout>
</template>

<script>
import {
  QLayout,
  QToolbar,
  QToolbarTitle,
  QBtn,
  QIcon,
  QList,
  QField,
  QInput,
  QListHeader,
  QItem,
  QItemSide,
  QItemMain
} from 'quasar'
import STakePictured from './TakePictured.vue'
export default {
  name: 'index',
  data() {
    return {

    }
  },
  methods: {
    img(blob){
      // console.log(blob);
    }
  },
  components: {
    STakePictured,
    QLayout,
    QToolbar,
    QToolbarTitle,
    QBtn,
    QIcon,
    QList,
    QInput,
    QField,
    QListHeader,
    QItem,
    QItemSide,
    QItemMain
  }
}
</script>

<style lang="stylus">
</style>
