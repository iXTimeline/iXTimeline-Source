<template>
  <v-menu
    :close-on-content-click="false"
    bottom
    left
    min-width="300"
    max-width="300"
    nudge-left="12"
    offset-x
    transition="slide-y-transition"
  >
    <v-btn
      slot="activator"
      class="elevation-0"
      color="grey"
      dark
      fab
      fixed
      style="top: 96px;"
      top
    >
      <v-icon>mdi-settings</v-icon>
    </v-btn>
    <v-card>
      <v-container grid-list-xl>
        <v-layout wrap>
          <v-flex xs12>
            <div class="text-xs-center body-2 text-uppercase sidebar-filter">PLACEHOLDER FILTERS</div>
            <v-divider class="mt-3"/>
          </v-flex>
          <v-flex
            xs12
          >
            <div class="text-xs-center body-2 text-uppercase sidebar-filter">STUFF HERE</div>
          </v-flex>

          <v-flex xs12>
            <v-btn
              target="_blank"
              color="success"
              block
            >
              BUTTON
            </v-btn>
          </v-flex>
          <v-flex xs12>
            <v-btn
              target="_blank"
              class="white--text"
              color="primary"
              block
            >
              BUTTON
            </v-btn>
          </v-flex>

        </v-layout>
      </v-container>
    </v-card>
  </v-menu>
</template>

<script>
// Utilities
import {
  mapMutations,
  mapState
} from 'vuex'

export default {
  data: () => ({
    colors: [
      'primary',
      'info',
      'success',
      'warning',
      'danger'
    ],
  }),

  computed: {
    ...mapState('app', ['image', 'color']),
    color () {
      return this.$store.state.app.color
    }
  },

  methods: {
    ...mapMutations('app', ['setImage']),
    setColor (color) {
      this.$store.state.app.color = color
    }
  }
}
</script>

<style lang="scss">
  .v-avatar,
  .v-responsive {
    cursor: pointer;
  }
</style>
