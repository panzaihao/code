<template>
  <div id="app">
    <el-autocomplete
      v-model="state"
      :fetch-suggestions="querySearchAsync"
      placeholder="输入遇到的问题吧"
      hide-loading="ture"
      style="width: 700px;margin: 16px;border-radius: 20pt;overflew: hidden"
      @select="handleSelect"
    />
    <el-backtop target=".page-component__scroll .el-scrollbar__wrap" />
  </div>
</template>
<script src="https://unpkg.com/vue/dist/vue.js"></script>
<script src="https://unpkg.com/element-ui@2.13.2/lib/index.js"></script>
<script type="module">
export default {
  data() {
    return {
      questions: [],
      state: '',
      timeout: null
    }
  },
  mounted() {
    this.questions = this.loadAll()
  },
  methods: {
    loadAll() {
      return [
        { 'value': '页面无法正常显示，一片空白，出现报错：page is not defined',
          'reasons': '1.这往往是index.js里面Page的P没有大写导致的。2.也有可能是app.json文件里面页面路径配置不对。',
          'solve': '1.检查一下index.js文件中Page里面的P是否大写了。' + '2.检查一下app.json文件里面的路径配置' +
          '3.如果以上问题都已解决，但还是出现报错，那就说明在app.json文件里其他页面的路径有问题(如果有的话)。代码在有问题的页面卡住了，没有继续往后执行。 ' },
        { 'value': '给云函数安装依赖时怎么都不能成功',
          'reasons': '',
          'solve': ' 直接部署，不用安装，不会造成其他不良后果！如果不可行的话，那么试试检查自己的Node.JS是否安装成功，和自己的npm是否可以识别成功。' +
          '可以参考：https://blog.csdn.net/qq_36538012/article/details/85348019' },
        { 'value': '怎么改margin创造的边框的颜色（注：class=’margin’）',
          'reasons': '',
          'solve': ' 改颜色的话试试background-color属性' },
        { 'value': '已经显示正确的结果，但云函数那块（queey函数）还是有报错',
          'reasons': '1.修改云函数后忘记部署了。2. queey函数本身出现了一些小bug',
          'solve': ' 1.每次做修改后重新部署，并且刷新一下。2.仔细检查一下queey函数的内容是否正确（包含使用的逗号等）。' },
        { 'value': '什么时候用双引号什么时候用单引号？',
          'reasons': '',
          'solve': ' 都可以！有极少数情况会需要区分哟，感兴趣的话可以看看下面关于字符串部分的讲解: https://www.runoob.com/js/js-datatypes.html' },
        { 'value': ' js里注释是不能用<!--**-->但能用//吗',
          'reasons': '',
          'solve': ' <!--  -->用于 HTML；css 用 /**/；js /**/    //都可以；json文件没有注释！如果试图在json文件中添加注释，会直接标红线！(note:按ctrl+？就可以加注释了)' },
        { 'value': "（js文件中）xx定义为' '， 那' '代表了啥吗",
          'reasons': '',
          'solve': ' 其代表空字符串' },
        { 'value': '图片无法显示（音频无法播放）（会出现报错：Failed to load local image resource /images/xx.png）',
          'reasons': '1.图片（音频）的名称里面混有中文字符（包含中文逗号等），2.是在代码中图片（音频）的路径不对' +
          '3.可能图片的位置被无意间改动了。4.图片命名里可能有空格。',
          'solve': ' 1.检查一下图片（音频）的名称里面是否有中文字符，如果有，对图片（音频）文件重命名。2.检查一下代码里面所写的程序路径是否正确。' +
          '3.回想一下之前有没有把图片（音频）移动了位置，如果图片移动过位置的话，在小程序里面也要改相应的路径哟。4.注意这些路径里面的空格，可能会很坑！' },
        { 'value': '如何重返上一步操作？如何回退？',
          'reasons': '',
          'solve': ' ctrl+z' },
        { 'value': ' search云函数调用了数据库操作的api，为什么不直接在函数里调用api',
          'reasons': '',
          'solve': ' 这是可以的' },
        { 'value': ' 查找图片时往上翻文件夹是.. /，往下翻用什么？（如何用相对路径来使用名为xx的图片？）',
          'reasons': '',
          'solve': ' 往下翻用文件名就OK（e.g：../../images/gogogo.png）' },
        { 'value': ' 如何用绝对路径来使用名为xx的图片？',
          'reasons': '',
          'solve': ' 可以参考电脑文件的绝对路径，例如：C:\windows\system32\cmd.exe，是从盘开始的路径。' },
        { 'value': ' 为什么会提示"a"未定义呢（error：‘a’ is no defined）',
          'reasons': '1.在js文件中很可能是在没有定义变量a的情况下就使用了变量a。比如：Var b ，len   len=a+b ',
          'solve': ' 1.在js文件中尝试将a定义出来。2.可以试试在wxml文件中对变量a进行数据绑定。3.注意一下有没有与a相关的变量名打错了' },
        { 'value': ' 云控制台上显示了向数据库有读取记录，但是为什么hello集合里显示不出数据呢。昨天还可以，从今天开始就不行了？',
          'reasons': '',
          'solve': ' 1.试试重启，有可能是IDE问题。2.可能是在对数据进行删改的时候没有进行commit，切记要保存！' },
        { 'value': ' 云函数的回调结果不是预期的结果',
          'reasons': '1、有可能是在更改云函数代码后，没有重新部署 2、误改动原来的云函数',
          'solve': ' 1.重新部署云函数  2、仔细检查原来的函数是否正确' },
        { 'value': ' 为何在数据比较时出现报错？',
          'reasons': '1.数据绑定的方式不对。2.也有可能在比较时错误的用到了”=”。',
          'solve': ' 1.按照这样的形式进行数据绑定："{{xxx}}".2.试着检查一下是否出现了原因2中的情况，比较中有“=”号的地方替换为“==”号。' },
        { 'value': ' 请问读取时为何出错（提交正常，云数据库有正常显示）',
          'reasons': '1.有可能是将setData写成了诸如setData1，setData 2 这些不是自定义东西。（note: setData是原生语法）' +
          '2.函数内部嵌套时不能用this，如果这么做，会出现报错（this.setData is not a function; at api cloud. callFunction success callback function）',
          'solve': ' 1.检查是否出现类似于将setData写成setData1的情况。2.定义一个that来代替this的职责（note: var that=this）' },
        { 'value': ' 如何调整按钮大小?(字体的大小)',
          'reasons': '',
          'solve': ' 利用wxss中的属性size，设置size的值。E.g：size:30px;对于字体的大小，试试font-size属性。' +
          '具体可以参考：https://blog.csdn.net/wendyNo/article/details/105858153' },
        { 'value': ' 为何点击按钮后没有反应？',
          'reasons': '1.可能是js中与按钮反应相关的变量拼写错误。2.如果该按钮和页面跳转相关的话，有可能在app.json文件中没有对应的页面路径。' +
          '此外，在app.json文件中路径的名字不小心写错了也会出现类似的情况哟。',
          'solve': ' 1.检查js文件中与wxml文件中变量的拼写是否正确。2.检查一下对应路径有没有在app.json文件中。若没有，试试手动添加对应的路径。' +
          '其次，检查一下路径名是否正确，这个点很坑!' }

      ]
    },
    querySearchAsync(queryString, cb) {
      var questions = this.questions
      var results = queryString ? questions.filter(this.createStateFilter(queryString)) : questions

      clearTimeout(this.timeout)
      this.timeout = setTimeout(() => {
        cb(results)
      }, 3000 * Math.random())
    },
    createStateFilter(queryString) {
      return (state) => {
        return (state.value.toLowerCase().indexOf(queryString.toLowerCase()) === 0)
      }
    },
    handleSelect(item) {
      console.log(item)
    }
  }
}
</script>
