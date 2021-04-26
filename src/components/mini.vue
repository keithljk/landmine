<template src="@/components/mini.html"></template>

<script>
export default {
  data(){
    return {
      miniCount: 10,
      miniAry: [],
      numAry: [],
      dataAry: [],
      spaceAry: [],
      spaceAryFinish: [],
      boom: false,
      win: false,
      timer: 0,
      timeStart: 0
    }
  },
  mounted() {
    this.restart()
  },
  methods: {
    restart(){
      this.clearArray()
      this.createSpace()
      this.setMiniAry()
      this.setNumberAry()
      this.reSet()
    },
    createSpace(){
      for(let i=0;i<9;i++){
        let ary = []
        for(let j=0;j<9;j++){
          ary.push({x: i,y: j,z: ' '})
        }
        this.dataAry.push(ary)
      }
    },
    setMiniAry(){
      let miniObj = (row,col) => {
        return {
          row: row,
          col: col
        };
      }

      while(this.miniAry.length < this.miniCount){
        let row = Math.floor(Math.random()*10);
        let col = Math.floor(Math.random()*10);
        if(row < this.miniCount - 1 && col < this.miniCount - 1){
          if(this.miniAry.findIndex(item => item.row == row && item.col == col) == -1){
            this.miniAry.push(miniObj(row,col))
          }
        }
      }
    },
    setNumberAry(){
      for(let obj of this.miniAry){
        for(let i=-1; i<2; i++){
          if(obj.row + i > -1 && obj.row + i < 9){
            for(let j=-1; j<2; j++){
              if(!(i==0 && j==0)){
                obj.col + j > -1 && obj.col + j < 9 ? this.numAry.push({x: obj.row + i,y: obj.col + j,z: 0}) : console.log((obj.row + i)+','+(obj.col + j))
              }
            }
          }
        }
      }
    },
    reSet(){
      let result = [];
      this.numAry.forEach(item => {
        let index = result.findIndex(obj => obj.x == item.x && obj.y == item.y)
        if(index == -1){
          result.push({x: item.x,y: item.y,z: 1})
        }else{
          let z = result[index].z + 1
          result.splice(index,1,{x: item.x,y: item.y,z: z})
        }
      })
      this.numAry = result
    },
    clickResult(row,col){
      if(this.timer == 0){
        this.timeStart = setInterval(this.plusTimer, 1000)
      }
        let miniIndex = this.miniAry.findIndex(obj => obj.row == row && obj.col == col)
        let index = this.numAry.findIndex(obj => obj.x == row && obj.y == col) 

        if(miniIndex != -1){
          if(this.dataAry[row][col].z != 'p'){
            this.stopTimer()
            this.dataAry[row].splice(col,1,{x: row,y: col,z: 'X'})
            this.boom = true
          }
        }else{
          if(index == -1){
            if(this.spaceAryFinish.findIndex(obj => obj.x == row && obj.y == col) == -1){
              this.spaceAry.push({x: row,y: col})
              do {
                this.openSpace(this.spaceAry[this.spaceAry.length-1].x,this.spaceAry[this.spaceAry.length-1].y)
              } while (this.spaceAry.length > 0);
            }
          }else{
            this.dataAry[row].splice(col,1,{x: row,y: col,z: String(this.numAry[index].z)})
          }
        }
    },
    openSpace(row,col){
      let removeObj = this.spaceAry.pop()
      this.spaceAryFinish.push({x: removeObj.x,y: removeObj.y})
      for(let i=-1; i<2; i++){
        if(row + i > -1 && row + i < 9){
          for(let j=-1; j<2; j++){
            if(col + j > -1 && col + j < 9){
              if (this.spaceAryFinish.findIndex(obj => obj.x == row + i && obj.y == col + j) == -1){
                let index = this.numAry.findIndex(obj => obj.x == row + i && obj.y == col + j)
                index == -1 ? this.spaceAry.push({x: row + i,y: col + j}) : this.dataAry[row + i].splice(col + j,1,{x: row + i,y: col + j,z: String(this.numAry[index].z)})
              }
            }
          }
        }
      }
    },
    flag(row,col){
      if(this.dataAry[row][col].z == ' ' && this.miniCount > 0){
        if(this.spaceAryFinish.findIndex(obj => obj.x == row && obj.y == col) == -1){
          this.dataAry[row].splice(col,1,{x: row,y: col,z: 'p'})
          this.miniCount--
          if(this.miniCount == 0)this.finshGame()
        }
      }else if(this.dataAry[row][col].z == 'p'){
        this.dataAry[row].splice(col,1,{x: row,y: col,z: ' '})
        this.miniCount++
      }
    },
    finshGame(){
      let lb_notYet = false
      for(let i=0; i<this.dataAry.length; i++){
        for(let j=0; j<this.dataAry[0].length; j++){
          if(this.dataAry[i][j].z == "p"){
            if(this.miniAry.findIndex(obj => obj.row == this.dataAry[i][j].x && obj.col == this.dataAry[i][j].y) == -1){
              this.stopTimer()
              this.boom = true
              lb_notYet = true
            }
          }
        }
      }
      console.log(lb_notYet)
      if(!lb_notYet){
        this.stopTimer()
        console.log("WIN")
        this.win = true
      }
    },
    clearArray(){
      this.miniAry.splice(0, this.miniAry.length);
      this.numAry.splice(0, this.numAry.length);
      this.dataAry.splice(0, this.dataAry.length);
      this.spaceAry.splice(0, this.spaceAry.length);
      this.spaceAryFinish.splice(0, this.spaceAryFinish.length);
      this.boom = false
      this.win = false
      this.miniCount = 10
      this.timer = 0
      this.timeStart = 0
    },
    plusTimer(){
        this.timer++
    },
    stopTimer(){
        clearInterval(this.timeStart)
    }
  },
  computed: {

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.timer > span:last-child{
  position: absolute;
  right: 0;
}
.game{
  width: 100%;
  position: relative
}
.boom{
  display: flex;
  justify-content: space-around;
}
.win{
  display: flex;
  justify-content: space-around;
}
.content{
  display: flex;
  justify-content: space-around;
  border-top: 1px solid #bbb;
  border-left: 1px solid #bbb;
}
.row{
  flex: auto;
  width: 100%;
}
.col{
  border-right: 1px solid #bbb;
  border-bottom: 1px solid #bbb;
  line-height: 30px;
  height: 30px;
}
.btn{
  box-shadow: white 0 0px 1px, black 1px 0px 2px, white -1px 0px 2px;
  cursor: pointer;
}
</style>
