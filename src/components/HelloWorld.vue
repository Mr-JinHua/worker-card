<template>
  <div class="hello">
    <header>
      <el-dropdown @command="handleClick">
        <span class="el-dropdown-link">
          {{bgChoose}}<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item command="0">横向照片</el-dropdown-item>
          <el-dropdown-item command="1">竖向照片</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </header>
    <el-button>默认按钮</el-button>
    <canvas id="canvas" width="3508" height="2480"></canvas>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'HelloWorld',
  data() {
    return {
      bgChoose: '竖向照片',
      bgImg: [
        {
          name: '竖向照片',
          url: 'bgL.png'
        },
        {
          name: '横向照片',
          url: 'bgR.png'
        }
      ],
      imgLR: '竖向照片',
      msg: 'Welcome to Your Vue.js App',
      ctx: null,
      cardbg: 'bgL.png'
    }
  },
  mounted() {
    this.getData()
    this.initRender()
  },
  methods: {
    handleClick(e, s) {
      this.setBg(e)
      console.log(e, s, 'jdidi')
    },
    initRender() {
      const canvas = document.getElementById('canvas')
      this.ctx = canvas.getContext('2d')
      this.setBg()
    },
    setBg(wh = 0) {
      if (wh > 1 || wh < 0) wh = 0
      var img = new Image()
      img.src = `static/img/${this.bgImg[wh].url}`
      img.onload = function() {
        this.ctx.drawImage(img, 0, 0, 3508, 2480)
      }.bind(this)
    },
    getData() {
      axios.get('http://127.0.0.1:3000/honor',
        {
          params: {
            name: '张建新'
          }
        }
      ).then((res) => {
        console.log(res.data)
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.el-dropdown-link {
  cursor: pointer;
  color: #409EFF;
}
.el-icon-arrow-down {
  font-size: 12px;
}
</style>
