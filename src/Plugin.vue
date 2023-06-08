<template>
  <div class="List">
    <ul class="uk-list">
      <li
        v-for="(__, index) in model.items"
        :key="index"
        class="uk-margin"
      >
        <div class="uk-margin">
          <input
            v-model="model.items[index].title"
            class="uk-input uk-width-1-1"
            :aria-label="`List item ${index}`"
            placeholder="Title"
          >
        </div>
        <div class="uk-margin">
          <textarea
            v-model="model.items[index].text"
            class="uk-textarea uk-width-1-1"
            :aria-label="`List item ${index}`"
            placeholder="Description"
            rows="2"
          ></textarea>
        </div>
        <div>
          <a
            class="uk-text-small"
            aria-label="Remove item"
            @click="removeItem(index)"
            >
            <i class="uk-icon-minus-circle"></i> Remove item
          </a>
        </div>
      </li>
    </ul>

    <a
      class="uk-button uk-button-default uk-button-small uk-margin-small-top"
      @click="addItem"
    >
      <i class="uk-icon-plus-circle uk-margin-small-right"/>
      Add item
    </a>
  </div>
</template>

<script>
export default {
  mixins: [window.Storyblok.plugin],
  methods: {
    initWith() {
      return {
        // needs to be equal to your storyblok plugin name
        plugin: 'storyblok-list-fieldtype',
        items: [],
      }
    },
    addItem() {
      this.model.items.push({
        title: "",
        text: ""
      });
    },
    removeItem(index) {
      this.model.items = this.model.items.filter((_, i) => i !== index);
    },
    handleize(string) {
      return string.toLowerCase().replace(/[^\w\u00C0-\u024f]+/g, "-").replace(/^-+|-+$/g, "");
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
  .List__list {
    padding-left: 0;
  }

  .List__list-item + .List__list-item {
    margin-top: 5px;
  }
</style>
