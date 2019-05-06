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
          {{nowMsg.name}}<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item v-for="(item, index) in msg" :key="index" :command=index>{{item[1]}}</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
      <el-button type="primary" @click="imgOutput">导出图片</el-button>
    </header>
    <canvas id="canvas" width="3508" height="2480" @click="canvasCli"></canvas>
    <div id="img"></div>
  </div>
</template>

<script>
import axios from 'axios'
// import { setTimeout } from 'timers';

function numdays(date) {
  // Math.round
  return parseInt(Math.round((new Date() * 1 - new Date(date) * 1) / 86400000 / 365) * 365)
}
function liveThrough(date) {
  let workdate = new Date(date) * 1
  let dateLive = ['2019/3/29', '2018/11/30', '2015/12/28', '2014/9/17', '2013/9/13']
  let live = ['NC Cloud 1903升级', 'NC Cloud 1811首发版', 'NC6.5升级', 'NCV6.33升级', 'NCV6.3升级']
  let livenow = null
  let worklive = []
  let dateLivenew = dateLive.map((item, index) => {
    let newitme = item.replace(/\//g, '-')
    return new Date(newitme) * 1
  })
  for (let i = 0; i < dateLivenew.length; i++) {
    if (workdate > dateLivenew[i]) break
    livenow = i + 1
  }
  for (let i = 0; i < livenow; i++) {
    worklive.push(live[i])
  }
  return worklive
}

let criticNameInit = [3400, 1500]
let criticContentInit = [2030, 1400]

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
      // criticNameInit = [4006, 748]
      // criticContentInit = [4006, 748]
      point: {
        name: [270, 485],
        partyment: [186, 853],
        date: [710, 625],
        department: [320, 760],
        departments: [450, 895],
        live: [352, 935],
        bug: [810, 1680],
        honor: [352, 1320],
        self: [352, 1430],
        critic: {
          first: {
            name: [criticNameInit[0], criticNameInit[1]],
            content: [criticContentInit[0], criticContentInit[1]]
          },
          second: {
            name: [criticNameInit[0], criticNameInit[1] + 200],
            content: [criticContentInit[0] - 100, criticContentInit[1] + 200]
          },
          third: {
            name: [criticNameInit[0], criticNameInit[1] + 400],
            content: [criticContentInit[0] - 100, criticContentInit[1] + 400]
          },
          fourth: {
            name: [criticNameInit[0], criticNameInit[1] + 600],
            content: [criticContentInit[0] - 100, criticContentInit[1] + 600]
          }
        }
      },
      nowMsg: {
        bgChoose: '竖向照片',
        cardbg: 'bgR.png',
        name: '请选择',
        date: null,
        department: '',
        departments: [],
        award: null,
        bug: '',
        critic: {
          first: {
            name: '',
            content: '',
            contentArr: []
          },
          second: {
            name: '',
            content: '',
            contentArr: []
          },
          third: {
            name: '',
            content: '',
            contentArr: []
          },
          fourth: {
            name: '',
            content: '',
            contentArr: []
          }
        }
      }
    }
  },
  mounted() {
    this.getData()
    this.initRender()
  },
  methods: {
    imgOutput() {
      var image = new Image()
      image.src = this.canvas.toDataURL('image/png')
      console.log(image)
      document.getElementById('img').appendChild(image)
    },
    drawImg() {
      var iconimg = new Image()
      iconimg.src = `/static/icon/${this.nowMsg.name}.jpg`
      iconimg.onload = () => {
        if (this.nowMsg.cardbg === 'bgR.png') {
          this.ctx.drawImage(iconimg, 1890, 160, 750, 1000)
        } else {
          this.ctx.drawImage(iconimg, 1900, 190, 1150, 750)
        }
        this.ctx.closePath()
      }
    },
    drawLive() {
      let lineHeight = 90
      let liveArr = liveThrough(this.nowMsg.date)
      let liveStr = []
      let newStr = ''
      let point = [...this.point.live]
      console.log(point)
      liveArr.push('等诸多产品大事记')
      liveArr.forEach((item, index) => {
        for (let i in item) {
          if (this.ctx.measureText(newStr).width > 1200) {
            liveStr.push(newStr)
            newStr = ''
          }
          newStr += item[i]
          console.log(newStr)
        }
        if (index !== (liveArr.length - 1) && index !== (liveArr.length - 2)) newStr += '、'
      })
      liveStr.push(newStr)
      console.log(liveStr)
      liveStr.forEach((item, index) => {
        console.log(item, point)
        point[1] += lineHeight
        this.drawText({text: item, point: point, textAlign: 'left', color: '#88bf43'})
      })
    },
    drawAward() {
      let honorArr = this.nowMsg.award && this.nowMsg.award.honor
      let selfArr = this.nowMsg.award && this.nowMsg.award.self
      let honorStr = ''
      let selfStr = ''
      for (let i = 0; i < honorArr.length; i++) {
        honorStr += honorArr[i] + '、'
        if (this.ctx.measureText(honorStr).width > 1000) {
          break
        }
      }
      for (let i = 0; i < selfArr.length; i++) {
        selfStr += selfArr[i] + '、'
      }
      if (honorStr.length) {
        let newHonor = honorStr.slice(0, -1) + `等荣耀……`
        this.drawText({text: newHonor, point: this.point.honor, textAlign: 'left', color: '#48BED9'})
      }
      if (selfStr.length) {
        let newSelf = selfStr.slice(0, -1) + '等荣誉……'
        this.drawText({text: newSelf, point: this.point.self, textAlign: 'left', color: '#48BED9'})
      }
    },
    nameSelect(e) {
      let msg = this.msg
      this.nowMsg.department = msg[e][0]
      this.nowMsg.name = msg[e][1]
      this.nowMsg.date = msg[e][2]
      // 评论区内容
      this.nowMsg.critic.first.name = msg[e][3]
      this.nowMsg.critic.first.content = msg[e][4]
      this.nowMsg.critic.second.name = msg[e][5]
      this.nowMsg.critic.second.content = msg[e][6]
      this.nowMsg.critic.third.name = msg[e][7]
      this.nowMsg.critic.third.content = msg[e][8]
      this.nowMsg.critic.fourth.name = msg[e][9]
      this.nowMsg.critic.fourth.content = msg[e][10]
      this.setBg()
    },
    drawCriti() {
      let point = this.point.critic
      let critic = this.nowMsg.critic
      // 评论名称
      this.drawText({text: '--' + critic.first.name, point: point.first.name, textAlign: 'right', fontSize: 48, color: '#555555'})
      this.drawText({text: '--' + critic.second.name, point: point.second.name, textAlign: 'right', fontSize: 48, color: '#555555'})
      this.drawText({text: '--' + critic.third.name, point: point.third.name, textAlign: 'right', fontSize: 48, color: '#555555'})
      this.drawText({text: '--' + critic.fourth.name, point: point.fourth.name, textAlign: 'right', fontSize: 48, color: '#555555'})
      // 评论内容
      this.dealCriti(critic.first.content, point.first.content, false)
      this.dealCriti(critic.second.content, point.second.content, true)
      this.dealCriti(critic.third.content, point.third.content, true)
      this.dealCriti(critic.fourth.content, point.fourth.content, true)
    },
    dealCriti(text = '无评论', point, any = true) {
      let rowWidth = any ? 1400 : 1300
      let textArr = text ? text.split('') : []
      let newText = ''
      let rowArr = []
      for (let i = 0; i < textArr.length; i++) {
        if (this.ctx.measureText(newText).width > rowWidth) {
          rowArr.push(newText)
          newText = ''
        }
        newText += textArr[i]
      }
      rowArr.push(newText)
      rowArr.forEach((txt, index) => {
        let newPoint = [point[0], point[1] + (index * 100)]
        this.drawText({text: txt, point: newPoint, textAlign: 'left', fontSize: 38, color: '#555'})
      })
    },
    drawDepartment() {
      this.drawText({text: this.nowMsg.department + '的一员', point: this.point.department, textAlign: 'left'})
    },
    drawDays() {
      let days = numdays(this.nowMsg.date)
      this.drawText({text: days, point: this.point.date})
    },
    draw() {
      this.drawText({text: this.nowMsg.name, point: this.point.name})
      this.drawDays()
      this.drawDepartment()
      this.drawCriti()
      this.drawLive()
      this.drawImg()
      this.getData(this.nowMsg.name)
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
    drawBug() {
      if (this.nowMsg.bug.length < 3) {
        this.ctx.fillStyle = '#fff'
        this.ctx.fillRect(180, 1600, 1600, 100)
        return
      }
      this.drawText({text: this.nowMsg.bug, point: this.point.bug, textAlign: 'left'})
    },
    drawDepartments() {
      let departments = this.nowMsg.departments

      if (departments.length < 1) {
        this.ctx.fillStyle = '#fff'
        this.ctx.fillRect(180, 800, 1600, 100)
        return
      }
      let txtStr = ''
      for (let i = departments.length - 1; i >= 0; i--) {
        if (i === 0) {
          txtStr += departments[i]
        } else {
          txtStr += departments[i] + '、'
        }
        if (this.ctx.measureText(txtStr).width > 800) break
      }
      // let newStr = txtStr
      let newStr = txtStr.slice(0, -1)
      newStr += '等部门'
      this.drawText({text: newStr, point: this.point.departments, textAlign: 'left'})
    },
    getData(name) {
      axios.get('http://10.6.244.89:3000/honor',
        {
          params: {
            name: name
          }
        }
      ).then((res) => {
        console.log(res.data)
      })
      axios.get('http://10.6.244.89:3000/bug',
        {
          params: {
            name: name
          }
        }
      ).then((res) => {
        this.nowMsg.bug = res.data
        this.drawBug()
      })
      axios.get('http://10.6.244.89:3000/mes').then((res) => {
        this.msg = res.data
      })
      axios.get('http://10.6.244.89:3000/awa',
        {
          params: {
            name: name
          }
        }
      ).then((res) => {
        this.nowMsg.award = res.data
        console.log(res.data)
        this.drawAward()
      })
      axios.get('http://10.6.244.89:3000/dep',
        {
          params: {
            name: name
          }
        }
      ).then((res) => {
        let newData = res.data
        newData.pop()
        console.log(newData)
        this.nowMsg.departments = newData
        this.drawDepartments()
      })
    },
    canvasCli(e) {
      let box = this.canvas.getBoundingClientRect()
      let x = box.top
      let y = box.left
      console.log(`${e.clientX - x},${e.clientY - y}`)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
header {
  height: 130px;
  margin: 20px ;
}
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
a {
  color: #42b983;
}
/deep/.el-dropdown {
  height: 100%;
  .el-icon-arrow-down {
    font-size: 12px;
  }
  .el-dropdown-link {
    width: 340px;
    display: inline-block;
    font-size: 40px;
    background: #66b1ff;
    line-height: 140px;
    text-align: center;
    color: white;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 20px;
    cursor: pointer;
  }
}
.el-dropdown-menu {
  .el-dropdown-menu__item {
    display: inline-block;
    height: 130px;
    line-height: 130px;
    font-size: 40px;
  }
}
.jinHua {
  width: 150px;
  height: 100px;
  opacity: 0;
}
.jinStyle {
  border: 1px solid red;
  display: inline-block;
}
.jinStyle div {
  list-style: none;
  text-decoration: none;
}
#upLoadImg img {
  width: 100%;
}
</style>
