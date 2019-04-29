<template>
  <div class="hello">
    <header>
      <el-dropdown @command="bgSelect">
        <span class="el-dropdown-link">
          {{nowMsg.bgChoose}}<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item command="0">竖向照片</el-dropdown-item>
          <el-dropdown-item command="1">横向照片</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
      <el-dropdown @command="nameSelect">
        <span class="el-dropdown-link">
          {{bgChoose}}<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item v-for="(item, index) in msg" :key="index" :command=index>{{item[1]}}</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </header>
    <canvas id="canvas" width="3508" height="2480" @click="canvasCli"></canvas>
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
          url: 'bgR.png'
        },
        {
          name: '横向照片',
          url: 'bgL.png'
        }
      ],
      canvas: null,
      ctx: null,
      msg: [],
      point: {
        name: [270, 485],
        partyment: [186, 853]
      },
      nowMsg: {
        bgChoose: '竖向照片',
        cardbg: 'bgL.png',
        name: ''
      }
    }
  },
  mounted() {
    this.getData()
    this.initRender()
  },
  methods: {
    canvasCli(e) {
      let box = this.canvas.getBoundingClientRect()
      let x = box.top
      let y = box.left
      console.log(e.clientX - x, e.clientY - y)
    },
    draw() {
      this.drawText({text: this.nowMsg.name, point: this.point.name})
      this.drawText({text: 'dian shang zi c', point: this.point.partyment, textAlign: 'left'})
      this.drawCriti({text: 'jiiijijij'})
      // drawCritiName()
    },
    // drawCritiName({name = '无评论',}) {
    //   this.drawText({text: name, point: [3704, 1108], textAlign: 'right'})
    // },
    drawCriti({text = '无评论', name = '无名字'}) {
      this.drawText({text: text, point: [100, 100]})
    },
    drawText({
      text, point, textAlign = 'center', fontsize = 55, color = '#F78C66'
    }) {
      this.ctx.font = `${fontsize}px 微软雅黑`
      this.ctx.textAlign = textAlign
      this.ctx.textBaseline = 'bottom'
      this.ctx.fillStyle = color
      this.ctx.fillText(text, point[0], point[1])
      this.ctx.closePath()
    },
    nameSelect(e) {
      this.nowMsg.name = this.msg[e][1]
      this.setBg()
    },
    bgSelect(e, s) {
      this.nowMsg.bgChoose = this.bgImg[e].name
      this.nowMsg.cardbg = this.bgImg[e].url
      this.setBg(e)
    },
    initRender() {
      this.canvas = document.getElementById('canvas')
      this.ctx = this.canvas.getContext('2d')
      this.setBg()
    },
    setBg(wh = 0) {
      if (wh > 1 || wh < 0) wh = 0
      var img = new Image()
      img.src = `static/img/${this.nowMsg.cardbg}`
      img.onload = function() {
        this.ctx.drawImage(img, 0, 0, 3508, 2480)
        this.draw()
      }.bind(this)
    },
    getData() {
      axios.get('http://127.0.0.1:3000/honor',
        {
          params: {
            name: '薛豪'
          }
        }
      ).then((res) => {
        console.log(res.data)
      })
      axios.get('http://127.0.0.1:3000/mes',
        {
          params: {
            name: '马征'
          }
        }
      ).then((res) => {
        console.log(res.data)
        this.msg = res.data
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
.el-dropdown-link {
  width: 200px;
  display: inline-block;
  height: 75px;
  font-size: 40px;
  background: blue;
  line-height: 75px;
  text-align: center;
  color: white;
}
</style>
