<template>
   <v-layout row fill-height>
      <v-flex class="settings" md7>
         <div class="settings__top my-2">
            <h2>Button style</h2>
            <v-spacer/>
            <v-btn depressed small class="ma-0" @click="reset_style()" color="error">RESET STYLE</v-btn>
         </div>
         <div class="new_attr">
            <v-text-field label="New property" v-model="new_attr.name"></v-text-field>
            <div class="new_attr__value">
               <v-text-field label="Value" v-model="new_attr.val" @keyup.enter="add_attr()"></v-text-field>
               <v-btn class="ma-0" flat icon @click="add_attr()">
                  <v-icon>add</v-icon>
               </v-btn>
            </div>
         </div>
         <div class="property_row" v-for="(style,index) in current_style" :key="index">
            <div v-if="index.indexOf('Color') == -1 && index.indexOf('color')">
               <v-text-field v-model="current_style[index]" :label="index"></v-text-field>
               <v-btn class="ma-0" flat icon @click="delete_attr(index)">
                  <v-icon>delete_forever</v-icon>
               </v-btn>
            </div>
            <div class="py-2 my-1" v-else>
               <p>{{index}}</p>
               <input type="color" v-model="current_style[index]">
               <v-btn class="my-0 mr-0" flat icon @click="delete_attr(index)">
                  <v-icon>delete_forever</v-icon>
               </v-btn>
            </div>
         </div>
      </v-flex>

      <v-flex class="preview" xs6 pa-2>
         <v-layout class="preview" align-center justify-space-between column fill-height>
            <div class="preview__top" style="width:100%">
               <h2 class="my-1">Button preview</h2>
               <v-tabs grow class="px-0" show-arrows>
                  <v-tabs-slider color="cyan"></v-tabs-slider>
                  <v-tab @click="change_style('default')">All</v-tab>
                  <v-tab @click="change_style('hover')">Hover</v-tab>
                  <v-tab @click="change_style('active')">Active</v-tab>
               </v-tabs>
            </div>
            
            <button :style="current_style">{{button_text}}</button>

            <v-card color="#26c6da" style="width: 100%;">
               <v-card-title row class="pa-0">
                  <span class="ml-3 font-weight-medium title font-weight-light">CSS</span>
                  <v-spacer/>
                  <v-btn depressed @click="copy()">copy css</v-btn>
               </v-card-title>
               <v-card-text id="code_text">{{css_code}}</v-card-text>
            </v-card>
         </v-layout>
      </v-flex>
   </v-layout>
</template>

<script>
export default {
   data() {
      return {
         current_style_state: 'default',
         current_style: {
            
         },
         button_text: "Button",
         new_attr: {
            name: "",
            val: ""
         },
         styles : {
            reset: {
               padding: "10px",
               backgroundColor: "#ffffff",
               boxShadow: "0 3px 5px #0000004f, 0 5px 12px #0000003b",
               borderRadius: "3px",
               fontWeight: '500'
            },
            default : {
               padding: "10px",
               backgroundColor: "#ffffff",
               boxShadow: "0 3px 5px #0000004f",
               borderRadius: "3px",
               fontWeight: '500'
            },
            hover: {
               padding: '10px',
               backgroundColor: '#ffffff',
               boxShadow: '0 3px 5px #0000004f',
               borderRadius: '3px',
               fontWeight: '500',
               color: '#ff2d32',

            },
            active: {
               padding: '10px',
               backgroundColor: '#ffe6e7',
               boxShadow: '0 3px 5px #0000004f, 0 10px 20px #0000003b',
               borderRadius: '3px',
               fontWeight: '500',
               color: '#ff2d32',
            }
         }
         
      };
   },
   methods: {
      add_attr() {
         let attr_name = this.new_attr.name.replace(/\s+/g, "");
         let attr_val = this.new_attr.val;
         this.$set(this.current_style, attr_name, attr_val);
         this.new_attr.name = "";
         this.new_attr.val = "";
      },
      delete_attr(key) {
         this.$delete(this.current_style, key);
      },
      copy() {
         let text = this.css_code;
         const textArea = document.createElement("textarea");
         textArea.style.position="fixed";
         textArea.style.left = '-9999px';
         textArea.value = text;
         document.body.appendChild(textArea);
         textArea.focus();
         textArea.select();
         var successful = document.execCommand("copy");
         document.body.removeChild(textArea);
         this.$emit("alert");
      },
      reset_style() {
         //this.current_style = this.styles.default;
         this.current_style = Object.assign({},this.styles.reset);
         this.styles[this.current_style_state] = Object.assign({}, this.styles.reset);
      },
      change_style(state){
         this.current_style_state = state;
         this.current_style = this.styles[state];
      }
   },
   computed: {
      css_code() {
         //parse and
         let code = "";
         let style = this.current_style;
         for (let attr in style) {
            code +=
               attr.replace(/([A-Z])/g, "-$1").trim() +
               ": " +
               style[attr] +
               ";\n";
         }
         return code.toLowerCase();
      }
   },
   watch: {
      current_style(event) {
         this.$emit('getData', this.styles);
      }
   },
   created(){
      this.change_style('default')
   }
};
</script>

<style lang="scss">
h2 {
   font-weight: 500 !important;
   text-transform: uppercase;
   font-size: 16px;
}

.settings {
   margin: 0 -10px 0 -10px;
   padding: 0 20px 0 20px;
   .settings__top {
      display: flex;
      align-items: center;
   }
   .new_attr__value {
      display: flex;
      align-items: center;
   }
   .property_row {
      margin: 0 -10px 0 -10px;
      padding: 0 10px 0 10px;
      align-self: center;
      align-items: center;
      border-radius: 0 3px 3px 0;
      &:nth-child(odd) {
         background-color: #ffffff15;
      }
      div {
         display: flex;
         align-items: center;
         p {
            margin: 0 5px 0 0;
         }

         button {
            justify-self: flex-end;
            margin-left: auto;
         }
      }
   }
}
.preview {
   button {
      color: black;
      transition: all 0.3s;

      &:hover,
      &::selection,
      &:focus {
         outline: 0;
      }
   }
   .v-card__title {
      color: #212121;
   }

   #code_text {
      font-family: "Fira Code Medium", monospace !important;
      font-size: 12px !important;
      text-align: left;
      line-height: 1.5 !important;
      white-space: pre-wrap !important;
      color: #212121;
      &::selection {
         color: cyan;
         background-color: #212121;
      }
   }
}
</style>
 