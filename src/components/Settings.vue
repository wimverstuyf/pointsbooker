<template>
  <form class="settings">
    <div class="input-wrapper">
      <label>Punten per dag</label>
      <input type="number" class="input" v-model="points"/>
    </div>
    <div class="input-wrapper">
      <label>Extra punten</label>
      <input type="number" class="input" v-model="extra"/>
    </div>
    <div class="input-wrapper">
      <label>Vrije dag</label>
      <v-select class="input-dropdown" :value="cheatday"
      :options="options.cheats" @input="setCheat" :searchable="false" :clearable=false></v-select>
    </div>
    <div class="input-wrapper">
      <button class="submit-btn" v-on:click="$emit('save', {points: points, extra: extra, cheat: cheat})">Beginnen</button>
    </div>
  </form>
</template>

<script>
export default {
  name: "Settings",
  props: {
    points: Number,
    extra: Number,
    cheat: Number
  },
  data: function() {
    return {
      options: {
        cheats: [
          { value: 0, label: 'Geen' },
          { value: 1, label: 'Maandag' },
          { value: 2, label: 'Dinsdag' },
          { value: 3, label: 'Woensdag' },
          { value: 4, label: 'Donderdag' },
          { value: 5, label: 'Vrijdag' },
          { value: 6, label: 'Zaterdag' },
          { value: 7, label: 'Zondag' }
        ]
      }
    };
  },
  computed: {
    cheatday: function() {
      return this.options.cheats[this.cheat];
    }
  },
  methods: {
    setCheat: function(value) {
      this.cheat = value.value;
    }
  }
};
</script>

<style lang="scss" src="../assets/styles/Settings.scss"/>
