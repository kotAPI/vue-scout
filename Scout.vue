<template>
            <div class="vue-scout-container" id="vue-scout">
                <div class="vue-scout-tag-item" v-for="(x,i) in value" :key="i">
                    <span class="tag-text-item">{{x[label]|| x}}</span>
                    <span class="vue-scout-tag-item-close-button" @click="removeClickedTag(i)">x</span>
                    
                </div>
                <input v-if="showInputState()" :placeholder="placeholder||'Search here..'" :size="searchTerm.length" type="text" class="vue-scout-input" v-model="searchTerm" v-on:keyup.enter="addToTags" @input="filterResults" @focus="focusOn=true" @blur="focusOn=true">

                <div class="tag-dropdown-container" v-if="focusOn">

                  <div v-for="(tag,index) in filterResults()" :class="addClassesToDropDownElements(index)" @click="addDropDownTagToSelectedTags(tag)" :key="index">
                    <div style="padding:4px;">{{tag[label] || tag}}</div>
                    <div class="remove-element-helpertext"> (remove {{label!==undefined?label:'element'}} )</div>
                  </div>
                  <div  v-if="filterResults().length===0">
                    <div class="tag-no-results">No results found</div>
                    <div v-if="taggable===true" class="click-to-add-tag-text" @click="emitNewTag">Hit enter or click here to add new tag</div>
                  </div>
                </div>
            </div>
</template>

<script>
export default {
  props: ["options", "label", "multiple", "value","placeholder","taggable"],
  data() {
    return {
      searchTerm: "",
      filteredTags: [],
      focusOn: false
    };
  },
  methods: {
    showInputState(){
      if(this.multiple===false){
        if(this.value.length>0){
          return false
        }
      }
      return true
    },
    addClassesToDropDownElements(index) {
      var classes = ["tag-dropdown"];
      /*  */
      if (this.label === undefined) {
        // do something
      } else {
        let element = this.filterResults()[index][this.label];

        for (var i = 0; i < this.value.length; i++) {
          if (this.value[i][this.label] === element) {
            classes.push("vue-dropdown-remove-element");
            break;
          }
        }
      }
      /*  */
      return classes.join(" ");
    },

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
    removeTagIfAlreadyExistsInList(tag) {
      for (var i = 0; i < this.value.length; i++) {
        if (this.value[i][this.label] === tag[this.label]) {
          this.value.splice(i, 1);
          this.focusOn = false;
          this.$emit("input", this.value);
          return true;
        }
      }
      return false;
    },
    addDropDownTagToSelectedTags(tag) {
      if (this.removeTagIfAlreadyExistsInList(tag)) {
        return;
      }
      
      if (this.multiple === false || this.multiple === undefined) {
        if (this.value.length >= 0) {
          this.value = [];
          this.value.push(tag);
          this.searchTerm = "";
          this.focusOn = false;
          this.$emit("input", this.value);
          return;
        }else{

        }
      }
      else if (this.multiple === true) {
        this.value.push(tag);
        this.focusOn = false;
        this.searchTerm = "";
        this.$emit("input", this.value);
      }
    },
    removeClickedTag(index) {
      this.value.splice(index, 1);
    },
    emitNewTag(){
      this.$emit("tag",this.searchTerm)
      this.focusOn=false;
      this.searchTerm = "";
    },
    addToTags() {
      if (!this.taggable) {
        return;
      }
      if (this.searchTerm === undefined) {
        return;
      }
      this.emitNewTag()

    },
    handleClick(e) {
      if (document.getElementById("vue-scout").contains(e.target)) {
        // Clicked in box
      } else {
        // Clicked outside the box
        this.focusOn = false;
      }
    }
  },
  beforeDestroy() {
    window.removeEventListener("click", this.handleClick);
  },
  mounted() {
    /* Click handler to detect clicks outside the box, so you can toggle the dropdown off */
    window.addEventListener("click", this.handleClick);
    /**/
  }
};
</script>



<style>
@import url("https://fonts.googleapis.com/css?family=Roboto");
.click-to-add-tag-text{
      background-color: #5bb883;
    text-align: center;
    padding: 10px;
    color: white;
    font-size: 0.8em;
    user-select: none;
    cursor: pointer;
}
.click-to-add-tag-text:hover{
  color:black;
}
.tag-no-results {
  text-align: center;
  margin-top: 3px;
  padding: 6px;
  color: #f44336;
  border-top: 1px solid #bfbfbf;
}
.vue-dropdown-remove-element > .remove-element-helpertext {
  display: block;
  padding: 3px 0px;
  color: black;
  font-weight: 300;
}
.remove-element-helpertext {
  display: none;
}
.vue-dropdown-remove-element {
  background-color: #ffbdb8 !important;
  color: black;
  font-weight: 500;
  font-style: italic;
}
.tag-dropdown-container {
  height: auto;
  max-height: 194px;
  overflow-y: scroll;
}
.tag-text-item{
  user-select: none;
      float: left;
    padding-right: 10px;
    display: inline;
    line-height: 24px;
}
.tag-dropdown:hover {
  background-color: #d4e8bc;
}
.tag-dropdown {
      width: 100%;
    background-color: white;
    padding: 9px 0px;
    border-bottom: 1px solid #d0d0d0;
    cursor: pointer;
    font-size: 0.8em;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    font-family: "Roboto", sans-serif;
    padding-left: 15px;
    box-sizing: border-box;
}
.vue-scout-tag-item-close-button {
  float: right;
  margin-right: 4px;
  display: inline-block;
  color: #d04f4f;
  font-size: 1.5em;
  cursor: pointer;
}
.vue-scout-tag-item-close-button:hover {
  color: black;
}
.vue-scout-tag-item {
      color: #333;
    background-color: #f3f3f3;
    border: 1px solid #dadada;
    border-radius: 4px;
    margin: 4px 1px 0 3px;
    padding: 3px 0.55em;
    font-family: "Roboto", sans-serif;
    min-height: 26px;
    font-size: 0.72em;
    display: inline-block;
}
.vue-scout-container {
  width: 100%;
  min-height: 30px;
  height: auto;
  margin: auto;
  display: block;
  border: 1px solid #dadada;
  border-radius: 10px;
  background-color: white;
}
.vue-scout-input {
      min-width: 16px;
    border: 0 none;
    background-color: transparent;
    margin: 0;
    outline: 0 none;
    height: 100%;
    font-family: "Roboto", sans-serif;
    padding: 7px 12px;
    min-height: 26px;
    display: block;
    clear: right;
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

