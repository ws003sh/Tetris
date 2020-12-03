<template>
<div class="space">
  <div class="row" v-for="(i, index) in list" :key="i.id">
    <span class="index">{{ index + 1 | fixIndex }}</span>
   <div v-for="j in i" :key="j.id" :class=" j === 0 ? 'item' : 'item haveValue'">
     {{j}}
   </div>
  </div>
  <div class="control">
    <p>{{ currentKey }}</p>
    <button @click="start">start</button>
    <button @keyup.up="handleKeyUp('up')">up</button>
    <button @keyup.down="handleKeyUp('down')">down</button>
    <button @keyup.left="handleKeyUp('left')">left</button>
    <button @keyup.right="handleKeyUp('right')">right</button>
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
    handleKeyUp (e) {
      console.log(e.key)
      this.currentKey = e.key
    },
    generateList () {
      let i = 0
      let j = 0
      let row = []
      while (i < 12) {
        row.push(0)
        i++
      }
      while (j < 20) {
        this.list.push(row)
        j++
      }
    },
    timeOut (count) {
      if (count < 21) {
        count++
        let self = this
        this.timer = setTimeout(function () {
          clearTimeout()
          this.timer = ''
          let a = [0, 0, 0, 0, 1, 1, 0, 0, 0, 0 ,0, 0]
          let b = [0, 0, 0, 0, 1, 1, 0, 0, 0, 0 ,0 ,0]
          let normal = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ,0 ,0]
          self.$set(self.list, count - 1, normal)
          self.$set(self.list, count, normal)
          self.$set(self.list, count, a)
          self.$set(self.list, count+1, b)
          self.timeOut(count)
        },
        750)
      } else {
        clearTimeout()
        this.timer = ''
      }
    },
    start () {
      console.log('start')
      // 放入一个方块，然后让其落下
      let a = [0, 0, 0, 0, 1, 1, 0, 0, 0, 0 ,0, 0]
      let b = [0, 0, 0, 0, 1, 1, 0, 0, 0, 0 ,0 ,0]
      this.$set(this.list, 0, a)
      this.$set(this.list, 1, b)
      this.timeOut(0)
    }
  },
  mounted () {
    this.generateList()
  },
  data () {
    return {
      list: [],
      currentKey: '',
      timer: ''
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
</style>
