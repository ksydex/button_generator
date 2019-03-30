<template>
   <div id="app">
      <v-flex xs8 md7>
         <v-card dark>
            <ui @getData="styleGetter" @alert="show_alert"></ui>
         </v-card>
         <v-card id="full_css" class="mt-2 mb-3" color="#26c6da" style="width: 100%;">
            <v-card-title row class="pa-0">
               <span class="ml-3 font-weight-medium title font-weight-light">FULL CSS</span>
               <v-spacer/>
               <v-text-field class="py-0 class_input" hint="Class name" v-model="class_name" placeholder="Class name"></v-text-field>
               <v-btn depressed @click="copy">copy css</v-btn>
            </v-card-title>
            <v-card-text id="code_text">{{full_css}}</v-card-text>
         </v-card>
         <v-alert class="alert_copied"
               :value="alert"
               color="green"
               type="success"
               transition="scale-transition"
            >Copied to clipboard!</v-alert>
      </v-flex>
   </div>
</template>

<script>
import ui from "./components/ui.vue";

export default {
   name: "app",
   components: {
      ui
   },
   data() {
      return {
         alert: false,
         styles: {},
         class_name: "btn"
      };
   },
   methods: {
      show_alert() {
         this.alert = true;
         setTimeout(() => (this.alert = false), 3000);
      },
      styleGetter(style) {
         this.styles = style;
      },
      copy() {
         let text = this.full_css;
         const textArea = document.createElement("textarea");
         textArea.style.position="fixed";
         textArea.style.left = '-9999px';
         textArea.value = text;
         document.body.appendChild(textArea);
         textArea.focus();
         textArea.select();
         var successful = document.execCommand("copy");
         document.body.removeChild(textArea);
         this.show_alert();
      },
   },
   computed: {
      full_css() {
         //parse to string
         let code = "";
         let styles = this.styles;
         let class_name = this.class_name;
         let states = [
            "." + class_name,
            "." + class_name + ":hover",
            "." + class_name + ":active"
         ];
         let i = 0;
         for (var style in styles) {
            if (style != "reset") {
               code += states[i] + " {\n";
               i++;

               let last_key = keys => {
                  keys = Object.keys(styles[style]);
                  return keys[keys.length - 1];
               };
               let last_attr = last_key();
               for (let attr in styles[style]) {
                  let ending = attr == last_attr ? ";" : ";\n";
                  code +=
                     "  " +
                     attr.replace(/([A-Z])/g, "-$1").trim() +
                     ": " +
                     styles[style][attr] +
                     ending;
               }
               code += "\n}\n";
            }
         }
         return code.toLowerCase();
      }
   }
};
</script>

<style lang="scss">
.btn{
  padding: 10px;
  background-color: #ffffff;
  box-shadow: 0 3px 5px #0000004f;
  border-radius: 3px;
  font-weight: 500;
}
.btn:hover{
  padding: 10px;
  background-color: #ffffff;
  box-shadow: 0 3px 5px #0000004f;
  border-radius: 3px;
  font-weight: 500;
  color: #ff2d32;
}
.btn:active{
  padding: 10px;
  background-color: #ffe6e7;
  box-shadow: 0 3px 5px #0000004f, 0 10px 20px #0000003b;
  border-radius: 3px;
  font-weight: 500;
  color: #ff2d32;
}

#app {
   -webkit-font-smoothing: antialiased;
   -moz-osx-font-smoothing: grayscale;
   text-align: center;
   margin-top: 60px;
   justify-content: center;
   display: flex;
}

.alert_copied {
   position: fixed !important;
   bottom: 10px;
   right: 10px;
   display: block;
}

#full_css {
   .v-card__title {
      color: #212121;
   }
   .v-btn {
      background-color: #212121;
      color: #fff;
   }

   .class_input {
      margin-top: -10px;
      width: 20px !important;

      .v-input__control {
         margin-bottom: -22px;
      }
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
