<template>
  <div class="List">
    <ol class="List__list uk-margin-bottom-remove">
      <li
        v-for="(item, index) in model.items"
        :key="index"
        class="List__list-item uk-flex uk-flex-middle"
      >
        <input
          v-model="model.items[index].title"
          class="uk-form-small uk-width-1-1"
          :aria-label="`List item ${index}`"
          placeholder="Title"
        >

        <input
          v-model="model.items[index].slug"
          class="uk-form-small uk-width-1-1 uk-margin-left"
          :aria-label="`List item ${index}`"
          placeholder="Slug"
        >
        <a
          class="assets__item-trash"
          aria-label="Remove item"
          @click="removeItem(index)"
          >
          <i class="uk-icon-minus-circle"></i>
        </a>
      </li>
    </ol>

    <a
      class="blok__full-btn uk-margin-small-top"
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
        plugin: 'storyblok-linklist-fieldtype',
        items: [],
      }
    },
    addItem() {
      this.model.items.push({
        title: "",
        slug: ""
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
