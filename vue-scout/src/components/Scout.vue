<template>
    <div class="vue-scout-container">
        <div id="vue-scout">
        <div class="vue-scout-tag-item" v-for="(x,i) in value" :key="i">
            <span class="tag-text-item">{{x[label]|| x}}</span>
            <span class="vue-scout-tag-item-close-button" @click="removeClickedTag(i)">x</span>
        </div>
        <input v-if="showInputState()" :placeholder="placeholder||'Search here..'" :size="searchTerm.length" type="text" class="vue-scout-input" v-model="searchTerm" v-on:keyup.enter="addToTags" @input="filterResults" @focus="focusOn=true" @blur="turnOffFocus">

    </div>
     <div class="tag-dropdown-container" v-if="focusOn">
          <div v-for="(tag,index) in filterResults()" :class="addClassesToDropDownElements(index)" @click="addDropDownTagToSelectedTags(tag)" :key="index">
            <div style="padding:4px;">
              {{tag[label] || tag}}
              <span >
                <span v-if="checkIfTagExistsInList(tag)" class="tag-select-text"  > Deselct</span>
                <span v-if="!checkIfTagExistsInList(tag)" class="tag-select-text"  >Select</span>
              </span>
            </div>
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
  props:{
    options:{
      default:()=>[],
      type:Array
    },
    label:{
      type:String,
      default:undefined
    },
    multiple:{
      type:Boolean,
      default:false
    },
    value:{
      type:Array,
      default:()=>[]
    },
    placeholder:{
      type:String,
      default:"Search here.."
    },
    taggable:{
      type:Boolean,
      default:false
    }
  },
  data() {
    return {
      searchTerm: "",
      filteredTags: [],
      focusOn: false
    };
  },
  methods: {
    turnOffFocus(){
      var interval = setTimeout(()=>{
        this.focusOn=false
      },200)


    },
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
        let element = this.filterResults()[index];
        // do something
        for (var i = 0; i < this.value.length; i++) {
          if (this.value[i] === element) {
            classes.push("vue-dropdown-remove-element");
            break;
          }
        }
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
      // So the parent knows that the input box is being inputted
      // useful in cases where you wanna clear out errors etc when user is retyping an error filled field etc.
      this.emitInputEvent()
      //
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
    checkIfTagExistsInList(tag){
      if(this.label===undefined){
        for (var i = 0; i < this.value.length; i++) {
          if (this.value[i] === tag) {
            /*So you dont modify the prop directly*, create a temporary array */
            
            return true;
          }
        }

      }else{
        for (var i = 0; i < this.value.length; i++) {
          if (this.value[i][this.label] === tag[this.label]) {
            /*So you dont modify the prop directly*, create a temporary array */
            
            return true;
          }
        }
        
      }
      return false;
    },
    removeTagIfAlreadyExistsInList(tag) {
      if(this.label===undefined){
        for (var i = 0; i < this.value.length; i++) {
          if (this.value[i] === tag) {
            /*So you dont modify the prop directly*, create a temporary array */
            var arr = this.value
            arr.splice(i, 1);
            this.focusOn = false;
            this.$emit("input", arr);
            return true;
          }
        }

      }else{
        for (var i = 0; i < this.value.length; i++) {
          if (this.value[i][this.label] === tag[this.label]) {
            /*So you dont modify the prop directly*, create a temporary array */
            var arr = this.value
            arr.splice(i, 1);
            this.focusOn = false;
            this.$emit("input", arr);
            return true;
          }
        }
        
      }
      return false;
      
    },
    addDropDownTagToSelectedTags(tag) {
     
      if (this.removeTagIfAlreadyExistsInList(tag)) {
        return;
      }
      
      if (this.multiple === false) {
        if (this.value.length >= 0) {
          var arr = [];
          arr.push(tag);
          this.searchTerm = "";
          this.focusOn = false;
          this.$emit("input", arr);
          return;
        }else{

        }
      }
      else if (this.multiple === true) {
        var arr = this.value
        arr.push(tag);
        this.focusOn = false;
        this.searchTerm = "";
        this.$emit("input", arr);
      }
    },
    removeClickedTag(index) {
      var arr = this.value

      arr.splice(index, 1);
      this.$emit("input", arr);
    },
    emitInputEvent(){
      this.$emit("typing")
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
    
  },
  beforeDestroy() {
    
  },
  mounted() {
    
  }
};
</script>



<style>
@import url("https://fonts.googleapis.com/css?family=Roboto");
.tag-select-text{
  display: none;
  color:black;
  font-size:0.65em;
  color:white;
  padding-right:20px;
  float:right;
}
.tag-dropdown:hover .tag-select-text{
  display:inline;
}
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
  background-color: #ec574c !important;
    color: white;
}
.tag-dropdown-container {
      height: auto;
    max-height: 194px;
    overflow-y: scroll;
    position: absolute;
    z-index: 100;
    width: 100%;
    border: 1px solid #dadada;
    border-radius: 10px;
    margin-top: 3px;
    box-sizing: border-box;
}
.tag-text-item{
  user-select: none;
    float: left;
    padding: 0px 9px;
    display: inline;
    line-height: 24px;
}
.tag-dropdown:hover {
  background-color: #78cc9c;
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
    padding: 1px 0.55em;
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
  position: relative;
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

