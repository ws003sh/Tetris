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
    <p>积分： {{ source }}</p>
  </div>
  <div>
    <p>
      <span>当前按钮：{{ currentKey }}</span>
      <br>
      <span>当前图形：{{ graphicalType }}</span>
    </p>
    <div>
      <div>
        <button @click="start">开始</button>
        <button @click="reset">重置</button>
      </div>
      <button @keyup.up="handleKeyUp('up')">旋转</button>
      <button @keyup.down="handleKeyUp('down')">下</button>
      <button @keyup.left="handleKeyUp('left')">左</button>
      <button @keyup.right="handleKeyUp('right')">右</button>
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
import Mixin from './mixin'
export default {
  mixins: [Mixin],
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
    rotateGraph () {
      // 顺时针90度旋转
      let graphRotate = this.graphRotate % 4 // 0 1 2 3
      switch (this.graphicalType) {
        case '田':
          // 不变
          break
        case '土':
          this.beforeMove()
          if (graphRotate === 1) {
            this.showGraphicalMatrix = [
              [1, 0],
              [1, 1],
              [1, 0]
            ]
          } else if (graphRotate === 0) {
            this.showGraphicalMatrix = [
              [0, 1, 0],
              [1, 1, 1]
            ]
          } else if (graphRotate === 2) {
            this.showGraphicalMatrix = [
              [1, 1, 1],
              [0, 1, 0]
            ]
          } else if (graphRotate === 3) {
            this.showGraphicalMatrix = [
              [0, 1],
              [1, 1],
              [0, 1]
            ]
          }
          this.move()
          break
        case 'L':
          this.beforeMove()
          if (graphRotate === 1) {
            this.showGraphicalMatrix = [
              [1, 1, 1, 1],
              [1, 0, 0, 0]
            ]
          } else if (graphRotate === 0) {
            this.showGraphicalMatrix = [
              [1, 0],
              [1, 0],
              [1, 0],
              [1, 1]
            ]
          } else if (graphRotate === 2) {
            this.showGraphicalMatrix = [
              [1, 1],
              [0, 1],
              [0, 1],
              [0, 1]
            ]
          } else if (graphRotate === 3) {
            this.showGraphicalMatrix = [
              [0, 0, 0, 1],
              [1, 1, 1, 1]
            ]
          }
          this.move()
          break
        case 'Z':
          this.beforeMove()
          if (graphRotate === 1) {
            this.showGraphicalMatrix = [
              [0, 1],
              [1, 1],
              [1, 0]
            ]
          } else if (graphRotate === 0) {
            this.showGraphicalMatrix = [
              [1, 1, 0],
              [0, 1, 1]
            ]
          } else if (graphRotate === 2) {
            this.showGraphicalMatrix = [
              [1, 1, 0],
              [0, 1, 1]
            ]
          } else if (graphRotate === 3) {
            this.showGraphicalMatrix = [
              [0, 1],
              [1, 1],
              [1, 0]
            ]
          }
          this.move()
          break
        case 'I':
          // graphRotate 取余数
          this.beforeMove()
          if (graphRotate === 1 || graphRotate === 3) {
            this.showGraphicalMatrix = [
              [1, 1, 1, 1]
            ]
          } else if (graphRotate === 2 || graphRotate === 0) {
            this.showGraphicalMatrix = [
              [1],
              [1],
              [1],
              [1]
            ]
          }
          this.move()
          // this.showGraphicalMatrix = this.I
          break
      }
    },
    reset () {
      this.matrix = []
      this.graphRotate = 0
      this.generateMatrix()
    },
    rotateSoil () {

    },
    handleKeyUp (e) {
      // console.log(e)
      this.currentKey = e.key
      if (this.showGraphicalMatrix.length === 0) return
      switch (e.key) {
        case 'ArrowRight':
          let graphicalWidth = this.showGraphicalMatrix[0].length // 测量图形的长度
          if (graphicalWidth + this.xOffset !== this.matrixWidth) {
            this.beforeMove()
            this.xOffset++
            this.move()
          }
          break
        case 'ArrowLeft':
          if (this.xOffset !== 0) {
            this.beforeMove()
            this.xOffset--
            this.move()
          }
          break
        case 'ArrowUp':
          // 旋转
          this.graphRotate++
          this.rotateGraph()
          break
        case 'ArrowDown':
          let graphicalHeight = this.showGraphicalMatrix.length
          if (this.yOffset + graphicalHeight < this.matrixHeight) {
            // 检测下一步是否有空间的
            if (!this.checkNextRow()) {
              this.checkRow()
              this.pushNewGraph()
            } else {
              this.beforeMove()
              this.yOffset++
              this.move()
              if (!this.checkNextRow()) {
                this.checkRow()
                this.pushNewGraph()
              }
            }
          } else {
            this.checkRow()
            this.pushNewGraph()
          }
          break
      }
    },
    checkNextRow () {
      // 检查下一行能否走下去
      let graphicalHeight = this.showGraphicalMatrix.length
      let res = 0
      for (let i = 0; i < this.showGraphicalMatrix[0].length; i++) {
        if (this.yOffset + graphicalHeight < this.matrixHeight) {
          if (this.matrix[this.yOffset + graphicalHeight][this.xOffset + i] === 1) {
            res++
          }
        }
      }
      if (res === this.showGraphicalMatrix[0].length) {
        return false
      } else {
        return true
      }
    },
    checkRow () {
      // 检查是否有行需要消除
      this.matrix.forEach((j, index) => {
        let num = 0
        j.forEach(k => {
          if (k === 1) {
            num++
          }
        })
        if (num === this.matrixWidth) {
          this.source = this.source + 100
          let row = []
          for (let i = 0; i < this.matrixWidth; i++) {
            row.push(0)
          }
          // 清除最后一行
          this.matrix.splice(index, 1)
          this.matrix.unshift(row)
        }
      })
    },
    generateMatrix () {
      // 生成矩阵
      let i = 0
      let j = 0
      while (j < this.matrixHeight) {
        let row = []
        while (i < this.matrixWidth) {
          row.push(0)
          i++
        }
        this.matrix.push(row)
        j++
        i = 0
      }
    },
    getNewGT () {
      // 随机选择新元素
      let num = Math.floor((Math.random()) * 5) // 0-5
      // console.log(this.graphicalTypeList[num])
      this.graphicalType = this.graphicalTypeList[num]
      this.setGraphicalMatrix()
      this.xOffset = 4
      this.yOffset = 0
      this.graphRotate = 0
    },
    pushNewGraph () {
      // 随机选择新元素
      this.getNewGT()
      // 插入新元素
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
    setGraphicalMatrix () {
      // 将对应的图形放入矩阵
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
    },
    start () {
      this.matrix = []
      this.generateMatrix()
      this.getNewGT()
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
      matrix: [], // 矩阵 宽 12 * 高 20
      currentKey: '', // 当前按下的按键是
      graphicalType: '田', // 当前放入的对象类型是 方块
      graphRotate: 0, // 图形基本旋转角度 默认为 0
      xOffset: 4, // 初始位置 x
      yOffset: 0, // 初始位置 y
      matrixWidth: 12, // 矩阵宽度
      matrixHeight: 10, // 矩阵高度
      showGraphicalMatrix: [], // 当前放入图形的数据
      source: 0, // 当前积分
      graphicalTypeList: ['田', '土', 'L', 'Z', 'I'] // 当前放入图形的数据
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import 'index.css'
</style>
