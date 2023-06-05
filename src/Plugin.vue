<template>
  <div class="integration">
      
      <div class="ft-checkbox">
        <input type="checkbox" id="backdrop" name="backdrop" value="backdrop" v-model="backdrop">
        <label for="backdrop">Backdrop</label>
      </div>

      <button @click="toggleIconDropdown()" class="ft-button">Select</button>
      <div class="ft-panel" v-if="openIconDropdown">
        <ul class="ft-grid">
          <li v-for="icon in icons" :key="icon.value" class="ft-grid__item">
            <button @click="selectIcon({ name: icon.name, value: icon.value })" :class="`ft-button ft-button--icon ${icon.value === selectedIcon ? 'ft-button--active' : '' }`">
              <div class="ft-panel__icon-holder">
                <img :src="`${options.domain}/icons/icon.${icon.value}.svg`" class="ft-panel__icon"/>
              </div>
            </button>
          </li>
        </ul>
      </div>

      <div class="ft-paragraph" v-if="model.value">
        Selected: {{ model.name }}
        <button @click="clearIcon()" class="ft-link">clear</button>
      </div>
  </div>
</template>

<script>
export default {
  mixins: [window.Storyblok.plugin],
  data() {
    return {
      openIconDropdown: false,
      icons: [],
      backdrop: false
    }
  },

  methods: {
    initWith() {
      return {
        // needs to be equal to your storyblok plugin name
        plugin: 'storyblok-icon-fieldtype',
        icons: [],
        name: null,
        value: null,
        backdrop: false
      }
    },
    pluginCreated() {
      this.getDataFromApi()
    },
    toggleIconDropdown() {
      this.openIconDropdown = !this.openIconDropdown

      this.getDataFromApi()
    },
    selectIcon({ name, value }) {
      if (value === this.selectedIcon) {
        this.clearIcon()

        return;
      }

      this.model.name = name
      this.model.value = value

      this.toggleIconDropdown()
    },
    clearIcon() {
      this.model.name = ''
      this.model.value = ''
    },
    async getDataFromApi() {
      const icons = await fetch('https://api.storyblok.com/v2/cdn/datasource_entries?datasource=icon&token=ecuaQkqnQS2nuhnRFNnpNgtt', { method: 'GET' })
      .then(response => response.json())
      .then(response => {
        return response.datasource_entries
      })
      .catch(err => console.error(err));

      this.icons = icons
    }
  },
  computed: {
    selectedIcon() {
      return this.model.value
    }
  },
  watch: {
    'model': {
      handler: function (value) {
        this.$emit('changed-model', value);
      },
      deep: true
    }
  }
}
</script>

<style> 
  .ft-button {
    background-color: #fff;
    border: 1px solid #ccc;
    cursor: pointer;
    height: 2rem;
    display: block;
    padding: 0 0.5rem;
    width: 100%;
    border-radius: 0.5rem;
  }

  .ft-button:hover,
  .ft-button--active {
    border-color: #00b3b0;
  }

  .ft-button--icon {
    height: auto;
    padding: 0.5rem;
  }

  .ft-panel {
    background-color: #fff;
    border: 1px solid #ccc;
    margin-top: 1rem;
    border-radius: 0.5rem;
    padding: 0.5rem;
  }

  .ft-grid {
    list-style: none;
    padding: 0;
    margin: 0;
    display: grid;
    grid-template-columns: repeat(5, minmax(0, 1fr));
    gap: 0.5rem;
    max-height: 10rem;
    overflow-y: auto;
  }

  .ft-panel__icon-holder {
    position: relative;
    padding-bottom: 100%;
  }
  
  .ft-panel__icon {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
  }

  .ft-paragraph {
    margin-top: 1rem;
  }

  .ft-link {
    background-color: transparent;
    border: none;
    padding: 0;
    appearance: none;
    text-decoration: underline;
    cursor: pointer;
  }

  .ft-checkbox {
    margin-bottom: 1rem;

    > label {
      margin-left: 0.5rem;
    }
  }
</style>
