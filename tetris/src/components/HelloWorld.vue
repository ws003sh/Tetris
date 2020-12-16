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
    <p>积分： 0</p>
  </div>
  <div>
    <p>
      <span>当前按钮：{{ currentKey }}</span>
      <br>
      <span>当前图形：{{ graphicalType }}</span>
    </p>
    <div>
      <button @click="start">start</button>
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
    <div>
      <p>旋转图形</p>
      <div>
        <button @click="changeGraphical('田')">左</button>
        <button @click="changeGraphical('I')">右</button>
        <button @click="test">test</button>
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
          if (this.yOffset !== 0) {
            this.beforeMove()
            this.yOffset--
            this.move()
          }
          break
        case 'ArrowDown':
          let graphicalHeight = this.showGraphicalMatrix.length
          if (this.yOffset + graphicalHeight < this.matrixHeight) {
            this.beforeMove()
            this.yOffset++
            this.move()
          } else {
            // 检查是否接触 检查是否需要消除该行
            this.pushNewGraph()
          }
          break
      }
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
      console.log(this.graphicalTypeList[num])
      this.graphicalType = this.graphicalTypeList[num]
      this.setGraphicalMatrix()
      this.xOffset = 4
      this.yOffset = 0
    },
    test () {
      // 验证随机数分布 floor 分布的更加均匀
      let list = []
      for (let i = 0; i < 10000; i++) {
        let num = Math.floor((Math.random()) * 5)
        list.push(num)
      }
      let res = {
        numOf_0: 0,
        numOf_1: 0,
        numOf_2: 0,
        numOf_3: 0,
        numOf_4: 0
      }
      list.forEach(i => {
        switch (i) {
          case 0:
            res.numOf_0++
            break
          case 1:
            res.numOf_1++
            break
          case 2:
            res.numOf_2++
            break
          case 3:
            res.numOf_3++
            break
          case 4:
            res.numOf_4++
            break
        }
      })
      console.log(res)
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
      // this.setGraphicalMatrix()
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
      // console.log('beforeMove')
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
      // console.log('move')
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
      xOffset: 4, // 初始位置 x
      yOffset: 0, // 初始位置 y
      matrixWidth: 12, // 矩阵宽度
      matrixHeight: 20, // 矩阵高度
      showGraphicalMatrix: [], // 当前放入图形的数据
      graphicalTypeList: ['田', '土', 'L', 'Z', 'I'], // 当前放入图形的数据
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
@import 'index.css'
</style>
