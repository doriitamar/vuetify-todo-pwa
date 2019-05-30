<template>
  <v-list-tile class="food-item">
    <v-list-tile-action>
      <v-icon
        color="green"
        v-if="food.isOkay"
      >done</v-icon>
      <v-icon
        color="error"
        v-else
      >close</v-icon>
    </v-list-tile-action>
    <template v-if="!editing">
      <v-list-tile-content>
        {{ food.text }}
      </v-list-tile-content>
      <v-list-tile-action>
      </v-list-tile-action>
    </template>
  </v-list-tile>
</template>

<script>

export default {
  props: ['food'],
  data () {
    return {
      editing: false
    }
  },
  directives: {
    focus (el, { value }, { context }) {
      if (value) {
        context.$nextTick(() => {
          context.$refs.input.focus()
        })
      }
    }
  }
}
</script>

<style lang="stylus">
.food-item
  .v-list__tile
    height: auto
    padding-top: 12px
    padding-bottom: 12px
  &.editing .v-list__tile
    height: 48px
</style>
