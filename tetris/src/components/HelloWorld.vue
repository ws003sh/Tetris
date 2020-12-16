<template>
<div class="view">
<div class="space">
  <div class="row" v-for="(i, index) in matrix" :key="i.id">
    <span class="index">{{ index + 1 | fixIndex }}</span>
   <div v-for="j in i" :key="j.id" :class=" j === 0 ? 'item' : 'item haveValue'">
     {{j}}
   </div>
  </div>
</div>
<div class="control">
  <div>
    <p>
      <span>当前按钮：{{ currentKey }}</span>
      <br>
      <span>当前图形：{{ graphicalType }}</span>
    </p>
    <div>
      <button @click="start(0, 0)">start</button>
      <button @click="reset">reset</button>
      <button @keyup.up="handleKeyUp('up')">up</button>
      <button @keyup.down="handleKeyUp('down')">down</button>
      <button @keyup.left="handleKeyUp('left')">left</button>
      <button @keyup.right="handleKeyUp('right')">right</button>
    </div>
  </div>
    <div>
      <p>设置类型</p>
      <div>
        <button @click="changeGraphical('田')">田</button>
        <button @click="changeGraphical('I')">I</button>
        <button @click="changeGraphical('L')">L</button>
        <button @click="changeGraphical('Z')">Z</button>
        <button @click="changeGraphical('土')">土</button>
      </div>
    </div>
  </div>
</div>
</template>

<script>
export default {
  name: 'HelloWorld',
  filters: {
    fixIndex (num) {
      if (num < 10) {
        return '0' + num
      } else {
        return num
      }
    }
  },
  created () {
    let self = this
    document.addEventListener('keyup', self.handleKeyUp)
  },
  methods: {
    reset () {
      this.matrix = []
      this.generateMatrix()
    },
    handleKeyUp (e) {
      // console.log(e)
      this.currentKey = e.key
      switch (e.key) {
        case 'ArrowRight':
          this.beforeMove()
          this.xOffset++
          this.move()
          break
        case 'ArrowLeft':
          this.beforeMove()
          this.xOffset--
          this.move()
          break
        case 'ArrowUp':
          this.beforeMove()
          this.yOffset--
          this.move()
          break
        case 'ArrowDown':
          this.beforeMove()
          this.yOffset++
          this.move()
          break
      }
    },
    generateMatrix () {
      // 生成矩阵
      let i = 0
      let j = 0
      while (j < 20) {
        let row = []
        while (i < 12) {
          row.push(0)
          i++
        }
        this.matrix.push(row)
        j++
        i = 0
      }
    },
    start () {
      // 放入一个方块
      switch (this.graphicalType) {
        case '田':
          this.showGraphicalMatrix = this.田
          break
        case '土':
          this.showGraphicalMatrix = this.土
          break
        case 'L':
          this.showGraphicalMatrix = this.L
          break
        case 'Z':
          this.showGraphicalMatrix = this.Z
          break
        case 'I':
          this.showGraphicalMatrix = this.I
          break
      }
      let yOffset = 0
      let xOffset = 4
      this.showGraphicalMatrix.forEach((i, index) => {
        i.forEach((j, jIndex) => {
          if (j === 1) {
            this.matrix[yOffset + index].splice(xOffset + jIndex, 1, 1)
          }
        })
      })
    },
    beforeMove () {
      // 移动前 删除对应的数据
      console.log('beforeMove')
      this.showGraphicalMatrix.forEach((i, index) => {
        i.forEach((j, jIndex) => {
          if (j === 1) {
            this.matrix[this.yOffset + index].splice(this.xOffset + jIndex, 1, 0)
          }
        })
      })
    },
    move () {
      // 移动方块
      console.log('move')
      this.showGraphicalMatrix.forEach((i, index) => {
        i.forEach((j, jIndex) => {
          if (j === 1) {
            this.matrix[this.yOffset + index].splice(this.xOffset + jIndex, 1, 1)
          }
        })
      })
    },
    changeGraphical (name) {
      this.graphicalType = name
    }
  },
  mounted () {
    this.generateMatrix()
  },
  data () {
    return {
      matrix: [], // 矩阵 宽12 * 高20
      currentKey: '', // 当前按下的按键是
      timer: '', // 计时器
      graphicalType: '田', // 当前放入的对象类型是 方块
      xOffset: 4,
      yOffset: 0,
      showGraphicalMatrix: [],
      土: [
        [0, 1, 0],
        [1, 1, 1]
      ],
      L: [
        [1, 0],
        [1, 0],
        [1, 0],
        [1, 1]
      ],
      I: [
        [1],
        [1],
        [1],
        [1]
      ],
      田: [
        [1, 1],
        [1, 1]
      ],
      Z: [
        [1, 1, 0],
        [0, 1, 1]
      ]
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.space {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  /* width: 400px; */
  border: 1px solid;
  border-bottom: 0;
}
.row {
  /* border: 1px solid; */
  border-bottom: 1px solid;
  display: flex;
}
.item {
  padding: 5px;
  width: 20px;
  height: 20px;
  border: 1px solid;
  border-bottom: 0;
  display: flex;
  align-items: center;
  justify-content: center;
}
.index {
  padding: 5px;
  border-right: 1px solid;
}
.haveValue {
  background-color: rgb(235, 190, 94);
  color: white;
}
.view {
  display: flex;
}
.control {
  display: flex;
  flex-direction: column;
  border: 1px solid;
}
.control div {
  margin-bottom: 20px;
}
.control button {
  color: white;
  background-color: red;
  border: 1px solid white;
  width: 60px;
  height: 30px;
}
</style>
