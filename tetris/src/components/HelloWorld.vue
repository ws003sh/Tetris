<template>
<div class="space">
  <div class="row" v-for="(i, index) in list" :key="i.id">
    <span class="index">{{ index + 1 | fixIndex }}</span>
   <div v-for="j in i" :key="j.id" :class=" j === 0 ? 'item' : 'item haveValue'">
     {{j}}
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
  methods: {
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
    }
  },
  mounted () {
    this.generateList()
  },
  data () {
    return {
      list: []
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
  width: 400px;
  border: 1px solid;
  border-bottom: 0;
}
.row {
  /* border: 1px solid; */
  border-bottom: 1px solid;
  display: flex;
}
.item {
  padding: 10px;
}
.index {
  padding: 10px;
  border-right: 1px solid;
}
.haveValue {
  background-color: #666;
}
</style>
