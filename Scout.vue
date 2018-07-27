<template>
    <div class="container">
        <div style="width:300px;">
            <div class="vue-multisect-container">
                <div class="vue-multisect-tag-item" v-for="(x,i) in tags" :key="i">
                    <span >{{x}}</span>
                    <span class="vue-multiselect-tag-item-close-button" @click="removeClickedTag(i)">x</span>
                </div>
                <input type="text" class="vue-multisect-input" v-model="searchTerm" v-on:keyup.enter="addToTags" @input="filterResults" @focus="focusOn=true" @blur="focusOn=true">
                <div class="" v-if="focusOn">
                  <div v-for="(tag,index) in filterResults()" class="tag-dropdown" @click="addDropDownTagToSelectedTags(tag)" :key="index">{{tag}}</div>
                </div>
            </div>
            
        </div>
    </div>
</template>

<script>
export default {
  data() {
    return {
      tags: [],
      searchTerm: "",
      taggable: false,
      multiple: false,
      allTags: [
        "pranay",
        "pranayyyyyy",
        "ppppraaanaayyyyy",
        "awesome",
        "illenium",
        "gryffin"
      ],
      filteredTags: [],
      currentTag: undefined,
      focusOn: false
    };
  },
  methods: {
    filterResults() {
      var tempArr = [];
      if (this.searchTerm.length === 0) {
        return this.allTags;
      }

      tempArr = this.allTags.filter(item => {
        if (item.toLowerCase().includes(this.searchTerm.toLowerCase())) {
          return item;
        }
      });
      return tempArr;
    },
    addDropDownTagToSelectedTags(tag) {
      if (this.multiple === false) {
        if (this.tags.length > 0) {
          this.tags = [];
          this.tags.push(tag);
          this.focusOn = false;
          return;
        }
      }
      this.tags.push(tag);
      this.focusOn = false;
    },
    removeClickedTag(index) {
      this.tags.splice(index, 1);
    },
    addToTags() {
      if (!this.taggable) {
        return;
      }
      if (this.currentTag === undefined) {
        return;
      }
      this.tags.push(this.currentTag);
      this.currentTag = undefined;
    }
  }
};
</script>



<style>
.tag-dropdown:hover {
  background-color: #8bc34a;
}
.tag-dropdown {
  width: 100%;
  background-color: white;
  padding: 5px;
}
.vue-multiselect-tag-item-close-button {
  padding-left: 4px;
  border-left: 1px solid #5c822f;
  margin-left: 8px;
  display: inline-block;
  color: white;
  cursor: pointer;
}
.vue-multiselect-tag-item-close-button:hover {
  color: black;
}
.vue-multisect-tag-item {
  display: inline-block;
  border-radius: 8px;
  border: 1px solid white;
  padding: 4px 12px;
  margin: 1px 2px;
  background-color: #8bc34a;
  color: black;
  font-family: monospace;
}
.vue-multisect-container {
  width: 100%;
  min-height: 30px;
  height: auto;
}
.vue-multisect-input {
  display: inline-block;
  min-width: 18px;
  border: 0 none;
  background-color: transparent;
  border-bottom: 1px solid #dedede;
  margin: 0;
  outline: 0 none;
  height: 100%;
  padding: 6px;
}
</style>

