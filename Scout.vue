<template>
    <div class="container">
        <div style="width:300px;background-color:white;">
            <div class="vue-multisect-container">
                <div class="vue-multisect-tag-item" v-for="(x,i) in tags" :key="i">
                    <span >{{x[label]|| x}}</span>
                    <span class="vue-multiselect-tag-item-close-button" @click="removeClickedTag(i)">x</span>
                </div>
                <input placeholder="look up here..." :size="searchTerm.length" type="text" class="vue-multisect-input" v-model="searchTerm" v-on:keyup.enter="addToTags" @input="filterResults" @focus="focusOn=true" @blur="focusOn=true">
                <div class="tag-dropdown-container" v-if="focusOn">
                  <div v-for="(tag,index) in filterResults()" class="tag-dropdown" @click="addDropDownTagToSelectedTags(tag)" :key="index">{{tag[label] || tag}}</div>
                  <div class="tag-no-results" v-if="filterResults().length===0">No Search Results found</div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import VueScout from "vue-scout";
export default {
  props: ["options", "label", "multiple", "value"],
  components: { VueScout },
  data() {
    return {
      tags: [],
      searchTerm: "",
      taggable: false,
      filteredTags: [],
      focusOn: false
    };
  },
  methods: {
    filterResults() {
      var tempArr = [];
      if (this.searchTerm.length === 0) {
        return this.options;
      }

      tempArr = this.options.filter(item => {
        if (item[this.label] !== undefined) {
          if (
            item[this.label]
              .toLowerCase()
              .includes(this.searchTerm.toLowerCase())
          ) {
            return item;
          }
        } else {
          if (item.toLowerCase().includes(this.searchTerm.toLowerCase())) {
            return item;
          }
        }
      });
      return tempArr;
    },
    addDropDownTagToSelectedTags(tag) {
      if (this.multiple === false || this.multiple === undefined) {
        if (this.tags.length > 0) {
          this.tags = [];
          this.tags.push(tag);
          this.searchTerm = "";
          this.focusOn = false;
          this.$emit("input", this.tags);
          return;
        }
      }
      if (this.multiple === true) {
        this.tags.push(tag);
        this.focusOn = false;
        this.searchTerm = "";
        this.$emit("input", this.tags);
      }
    },
    removeClickedTag(index) {
      this.tags.splice(index, 1);
    },
    addToTags() {
      if (!this.taggable) {
        return;
      }
      if (this.searchTerm === undefined) {
        return;
      }
      this.tags.push(this.searchTerm);
      this.searchTerm = "";
      this.$emit("input", this.tags);
    }
  }
};
</script>



<style>
@import url("https://fonts.googleapis.com/css?family=Roboto");
.tag-no-results {
  text-align: center;
  margin-top: 3px;
  padding: 6px;
  color: #f44336;
  border-top: 1px solid #bfbfbf;
}
.tag-dropdown-container {
  height: auto;
  max-height: 194px;
  overflow-y: scroll;
}
.tag-dropdown:hover {
  background-color: #8bc34a;
}
.tag-dropdown {
  width: 100%;
  background-color: white;
  padding: 4px 4px;
  border-bottom: 1px solid #d0d0d0;
  cursor: pointer;
  font-size: 0.8em;
  user-select: none;
  font-family: "Roboto", sans-serif;
}
.vue-multiselect-tag-item-close-button {
  padding-left: 4px;
  border-left: 1px solid #5c822f;
  margin-left: 8px;
  display: inline-block;
  color: #f44336;
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
  font-size: 0.8em;
  font-family: "Roboto", sans-serif;
  background-color: #8bc34a;
  color: black;
}
.vue-multisect-container {
  width: 100%;
  min-height: 30px;
  height: auto;
}
.vue-multisect-input {
  display: inline-block;
  min-width: 16px;
  border: 0 none;
  background-color: transparent;
  border-bottom: 1px solid #dedede;
  margin: 0;
  outline: 0 none;
  height: 100%;
  font-family: "Roboto", sans-serif;
  padding: 6px;
}
.tag-dropdown-container::-webkit-scrollbar {
  width: 4px;
  background-color: transparent;
}

.tag-dropdown-container::-webkit-scrollbar-track {
  -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
}

.tag-dropdown-container::-webkit-scrollbar-thumb {
  background-color: grey;
  outline: 1px solid slategrey;
  border-radius: 3px;
}
</style>

