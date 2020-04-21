<template>
  <div id="app">
    <main>
      <panel headline="Puntenboeker">

        <settings
          v-if="settings.show"
          v-bind:points="settings.pointsPerDay"
          v-bind:extra="settings.extraPoints"
          v-bind:cheat="settings.cheatDay"
          v-on:save="updateSettings($event)"
          ></settings>

        <counter
          v-if="!settings.show"
          v-bind:points="settings.pointsPerDay"
          v-bind:extra="settings.extraPoints"
          v-bind:cheat="settings.cheatDay"></counter>
      </panel>
    </main>
  </div>
</template>

<script>
import Panel from './components/Panel'
import Settings from './components/Settings'
import Counter from './components/Counter'

export default {
  name: 'app',
  components: {
    Panel,
    Settings,
    Counter
  },
  data: function() {
    return {
      settings: {
        pointsPerDay: 23,
        extraPoints: 23,
        cheatDay: 7,
        show: true
      }
    }
  },
  computed: {
  },
  methods: {
    updateSettings: function(data) {
      this.settings.pointsPerDay = data.points;
      this.settings.extraPoints = data.extra;
      this.settings.cheatDay = data.cheat;
      this.settings.show = false;

      localStorage.settings_pointsPerDay = data.points;
      localStorage.settings_extraPoints = data.extra;
      localStorage.settings_cheatDay = data.cheat;
      localStorage.settings_show = 'no';
    }
  },
  mounted() {
    if (localStorage.settings_pointsPerDay) {
      this.settings.pointsPerDay = localStorage.settings_pointsPerDay*1;
    }
    if (localStorage.settings_extraPoints) {
      this.settings.extraPoints = localStorage.settings_extraPoints*1;
    }
    if (localStorage.settings_cheatDay) {
      this.settings.cheatDay = localStorage.settings_cheatDay*1;
    }
    if (localStorage.settings_show) {
      this.settings.show = localStorage.settings_show == 'yes';
    }
  }
}
</script>

<style lang="scss" src="./assets/styles/App.scss"/>
