<template>
  <div class="flex items-center gap-[10px]">
    <BoardModalBoxDelete
      v-if="showDeleteColor"
      title="Удалить цвет"
      text="Вы действительно хотите удалить цвет?"
      @cancel="showDeleteColor = false"
      @yes="onDeleteColor"
    />
    <PopMenu>
      <NavBarButtonIcon icon="menu" />
      <template #menu>
        <PopMenuItem
          @click="clickCompletedTasks"
        >
          {{ showCompletedTasks ? 'Скрыть завершенные задачи' : 'Показать завершенные задачи' }}
        </PopMenuItem>
        <PopMenuDivider />
        <PopMenuItem
          icon="edit"
          @click="clickEditColor"
        >
          Свойства цвета
        </PopMenuItem>
        <PopMenuItem
          v-if="canDelete"
          icon="delete"
          @click="clickDeleteColor"
        >
          Удалить цвет
        </PopMenuItem>
      </template>
    </PopMenu>
  </div>
</template>

<script>
import NavBarButtonIcon from '@/components/Navbar/NavBarButtonIcon.vue'
import PopMenu from '@/components/modals/PopMenu.vue'
import PopMenuItem from '@/components/modals/PopMenuItem.vue'
import PopMenuDivider from '@/components/modals/PopMenuDivider.vue'
import BoardModalBoxDelete from '@/components/Board/BoardModalBoxDelete.vue'

import { SELECT_COLOR, REMOVE_COLOR_REQUEST } from '@/store/actions/colors'
import { NAVIGATOR_REMOVE_COLOR } from '@/store/actions/navigator'

export default {
  components: {
    NavBarButtonIcon,
    PopMenu,
    PopMenuItem,
    PopMenuDivider,
    BoardModalBoxDelete
  },
  props: {
    colorUid: {
      type: String,
      default: ''
    },
    showCompletedTasks: {
      type: Boolean,
      default: false
    }
  },
  emits: ['popNavBar', 'toggleCompletedTasks'],
  data () {
    return {
      showDeleteColor: false
    }
  },
  computed: {
    color () {
      return this.$store.state.colors.colors[this.colorUid]
    },
    canDelete () {
      return this.$store.state.colors.mycolors[this.colorUid] !== undefined
    }
  },
  methods: {
    clickEditColor () {
      if (!this.$store.state.isPropertiesMobileExpanded) {
        this.$store.dispatch('asidePropertiesToggle', true)
      }
      this.$store.commit('basic', { key: 'propertiesState', value: 'color' })
      this.$store.commit(SELECT_COLOR, this.color)
    },
    clickDeleteColor () {
      this.showDeleteColor = true
    },
    onDeleteColor () {
      this.showDeleteColor = false
      //
      this.$store.dispatch(REMOVE_COLOR_REQUEST, this.colorUid)
        .then(() => {
          this.$store.dispatch('asidePropertiesToggle', false)
          this.$store.commit(SELECT_COLOR, undefined)
          //
          this.$store.commit(NAVIGATOR_REMOVE_COLOR, this.color)
          //
          this.$emit('popNavBar')
        })
    },
    clickCompletedTasks () {
      this.$emit('toggleCompletedTasks')
    }
  }
}
</script>

<style scoped>

</style>
