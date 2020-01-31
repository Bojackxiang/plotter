<template>
  <div id="app">
    <h2>Input</h2>
    <textarea v-model="Input">

    </textarea>
    <p style="color: red">{{ ErrorMsg }}</p>
    <h2>Output</h2>

    <svg  width="250" height="250" xmlns="http://www.w3.org/2000/svg" version="1.1" v-html="SvgHtml" >
    </svg>
  </div>
</template>

<script>

const CommandType = ['r','c','p','e']
function GetRandomColor() {
  let r =  Math.floor(Math.random() * 255);
  let g =  Math.floor(Math.random() * 255);
  let b =  Math.floor(Math.random() * 255);
  return `rgb(${r},${g},${b})`;
}
export default {
  name: 'app',
  data(){
    return {
      Input: undefined,
      ErrorMsg: "",
      SvgHtml: "",
      Colors: {
        r: GetRandomColor(),
        c: GetRandomColor(),
        p: GetRandomColor(),
        e: GetRandomColor(),
      }
    }
  },
  methods:{
    Rect(curr) {
      curr.splice(0,1)
      if(curr.length !== 4) {
        return false; 
      }
      if (isNaN(curr.join(""))) {
        return false; 
      }
      return `
        <rect x="${curr[0]}" y="${curr[1]}" width="${curr[2]}" height="${curr[3]}" fill="${this.Colors['r']}"/>
      `
    },
    Circle(curr) {
      curr.splice(0,1)
      if(curr.length !== 3) {
        return false; 
      }
      if (isNaN(curr.join(""))) {
        return false; 
      }
      return `
        <circle cx="${curr[0]}" cy="${curr[1]}" r="${curr[2]}" fill="${this.Colors['c']}"/>
      `
    },
    Polygon(curr) {
      curr.splice(0,1)
      return `
        <polygon points="${curr.join(" ")}" fill="${this.Colors['p']}"/>
      `
    },
    Ellipse(curr) {
      curr.splice(0,1)
      if(curr.length !== 4) {
        return false; 
      }
      if (isNaN(curr.join(""))) {
        return false; 
      }
      return `
        <ellipse cx="${curr[0]}" cy="${curr[1]}" rx="${curr[2]}" ry="${curr[3]}" fill="${this.Colors['e']}"/>
      `
    },
    Handler(val) {
      this.ErrorMsg  = ""
      let LinesData = val.split("\n");
      let OutPutHtml = '';
      for(let i=0;i < LinesData.length ; i++){
        if (!LinesData[i].trim()) {
          continue
        }
        let curr = LinesData[i].trim().split(" ");
        if (!curr.length) {
          continue
        }
        if (CommandType.indexOf(curr[0][0].toLocaleLowerCase()) === -1) {
          this.ErrorMsg = `Error: line ${i+1} has error`;
          return; 
        }

        let result; 
        switch(curr[0][0]){
          case CommandType[0]:
            result = this.Rect(curr)
            break
          case CommandType[1]:
            result = this.Circle(curr)
            break
          case CommandType[2]:
            result = this.Polygon(curr)
            break
          case CommandType[3]:
            result = this.Ellipse(curr)
            break
        }

        if(!result) {
          this.ErrorMsg = `Error: line ${i+1} has error`;
          return 
        }
        OutPutHtml += result
      }

      this.SvgHtml = OutPutHtml
    }
  },
  watch: {
    Input(newVal,oldVal) {
      this.ErrorMsg  = ""
      this.SvgHtml  = ""
      if(!newVal || newVal === '') {
        return
      }
      this.Handler(newVal)
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}

textarea {
  width: 800px;
  height: 80px;
  font-size: 16px;
  padding: 10px;
  resize: none
}
svg {
  width: 250px;
  height: 250px;
}
</style>
