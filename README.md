# music
音乐播放器
该项目模仿千千音乐  
![images](https://github.com/eamoney/music/blob/master/images/2.png)  
![images](https://github.com/eamoney/music/blob/master/images/7.png)  
![images](https://github.com/eamoney/music/blob/master/images/8.png)  
![images](https://github.com/eamoney/music/blob/master/images/4.png)  
一.配置路由    
   1.src下创建index 页面容易  包含五个子路由  
   2.本项目所有的接口数据都是来自百度音乐的接口
 
二.使用axios获取数据：  
1.这里的所有接口都是借用了百度音乐的接口 接口地址:www.wwwtliu.com/blog/  
 下载axios的包 npm i axios --save  
 在入口文件中引入   
 axios注册方法是添加到vue的原型中去的  
 Vue.prototype.$axios='axios'    
2.需要使用到代理  
 在config文件下index.js文件中的proxy对象配置
 "/baidu_music_api": {  
      target: "http://tingapi.ting.baidu.com",  
      changeOrigin: true,//是否开启跨域  
      pathRewrite: {  
          '^/baidu_music_api': ''  
      }  
 }
 三 今日推荐榜单页面    
 ![images](https://github.com/eamoney/music/blob/master/images/recommend.png)   
  1.创建组件TadayRecommend 作为home下的子组件 
  2.在home组件中引入并且挂载
  3.在子组件的mounted中用axios请求数据 获取到的数据存放在组件的data上  
    this.$axios.get(url)
     .then(res => {
     this.todayRecommend = res.data.song_list
    })
     .catch(error => {
  console.log(error);
    })
  4.循环遍历拿到的数据  
 四.新歌速递  
 ![images](https://github.com/eamoney/music/blob/master/images/newlist.png)  
   1.同上
   2.这里有一个轮播图的组件 由于没有轮播图图片的接口地址 这边我吧图片下载
     到本地    
   3.这里用到vue-awesome-swiper轮播图插件  
     ![images](https://github.com/eamoney/music/blob/master/images/swiper.png)  
     下载:npm i vue-awe-soome-swiper 
     新建一个轮播图组件  swiper  
     按需导入:    
     import 'swiper/dist/css/swiper.css'  
      import { swiper, swiperSlide } from 'vue-awesome-swiper'    
   4.复制样式结构 引入图片就可以使用了
   四.音乐榜单
   这里算是比较难处理的部分  涉及到三层的嵌套  
   ![images](https://github.com/eamoney/music/blob/master/images/1.png)  
   由图片可以看到  这是一个导航栏  也就是一个路由  
   不同的路由  对应不同的组件  
   1.创建一个MusicListNav组件 包裹我们的路由--最外层  
   ![images](https://github.com/eamoney/music/blob/master/images/qiantao.png)  
   2.由于这三个路由(hot new king)其实都是属于home下的子路由  
     所以分别创建这三个路由组件 然后在router下index.jx添加  
     在routes下的home下新增这几个路由  --第二层  
     ![images](https://github.com/eamoney/music/blob/master/images/qiantao2.png)  
   3.创建一个musiclist作为这三个路由的公用组件,各自引入 
     在三个路由中  --第三层
   4 因为要请求不同数据 分别定义路由各自的url地址属性 绑定属性的方式传递给   
     子组件  musiclist  
   5.子组件中propos接收到  父组件传来的url  
     通过对应的url  请求数据  
   优化:  如果是不停地切换hot/new/king这几个路由的话  
   每次切换  都会发起一次请求 页面都会刷新一次 所以我们可以吧 MusicListNav看成   是个动态组件  用keep-alive包裹起来缓存 这样每次切换就会从缓存中去请求 减少了页面http请求的次数  
   <keep-alive> <router-view /> </keep-alive>  
  五.热门歌单  
     同三  
  六.更多按钮的改造  
     ![images](https://github.com/eamoney/music/blob/master/images/more.png)  
     ![images](https://github.com/eamoney/music/blob/master/images/more2.png)  
     今日推荐/热歌榜单和热门歌单都有更多这个选项 点击能够显示更多的数据
     1.src pages下创建一个更多组件 morelist
     2.在ToayRecommend musiclist 组件中引入
     3.更多组件其实就是在组件中拿到点击的那个组件的url地址  
       然后修改url地址中的size参数  比如6修改成10 就能拿到更多数据  
       但我这做了一个修改 就是我把size写死 size=12 然后获取对应的type类型
       const moreListUrl = this.HOST + "/v1/restserver/ting?  method=baidu.ting.billboard.billList&type="+ this.$route.params.musictype +"&size=12&offset=0"
     组件部分做如下改造:
    因为更多是个公用组件 只需给到指定的属性便可以发起不同请求拿到不同数据  
    1.在home中为组件绑定 type 和title属性    
    <TodayRecommend title="今日榜单" type="1"/>  
    <TodayRecommend title="热门歌单" type="24"/>  
     2.在对应的组件中用props得到着两个值    
           props:{  
       title:{  
       type:String,  
       default:"今日榜单"  
     },  
       type:{  
       type:String,  
      default:"1"  
     }   
   },  
    3.改造axios请求的url地址   
   url = this.HOST + "/v1/restserver/ting?method=baidu.ting.billboard.billList&type="+   this.type +"&size=6&offset=0";  
    4.改造更多 将其变为一个router-link标签 并且传入参数  
    <router-link :to="{ name:'MoreList',params:{musictype:this.type,title:title}}"         tag="div">   更多</router-link>  
    5.在morelist中 用this.$route.parmas方式获取参数  
    6.定义morelist 中axios请求的url地址 便可以实现了  
    const moreListUrl = this.HOST + "/v1/restserver/ting?  method=baidu.ting.billboard.billList&type="+ this.$route.params.musictype +"&size=12&offset=  0"  
 六.播放页面的改造  
 ![images](https://github.com/eamoney/music/blob/master/images/musicplay.png)  
  很明显这里的播放页面是一个路由  而且是跟index同级的一个路由  
  所以就得在src下pages下创建 musicplay组件 并且在router下配置
  
  1.改造router-link 将所有要跳转到播放页的相关链接改造为router-link  

；
    例如： <router-link tag="div" :to="{name:'MusicPlay',params:{songid:item.song_id}}"   class="card url" v-for="(item,index) in newsMusic" :key="index">  
  这里需要把对应的songid传过来 每个链接的songid在其组件的for循环中就有了
  在musicplay中通过this.$route.parmas.songid获取 然后交给axios请求对应的数据  
  再渲染到页面上      
    
 2.使用audio标签播放歌曲 将动态属性：src绑定到audio标签上    
<audio ref="player" :src="currentUrl.bitrate.show_link" controls autoplay></audio>  
    特别注意的是 百度音乐接口中 http包含refer 防止盗链    
    所以仅仅是把获取到的地址链接传给audio标签 还是用不了的    
    要在index.html中 配置meta标签 解决百度音乐接口盗链问题    
  <meta name="referrer" content="never">    
 3.歌词的适配  （本项目中操作最麻烦的一个点）    

   首先把歌词单独分离成为一个LRC组件 作为musicplay的子组件  同样必须传入songid  
   这里要用到audio标签的属性 currenttime 和 duration  
 4.通过给audio标签添加ref属性 在vue中可以通过$refs像操作dom一样去操作
   
  <audio ref="player" :src="currentUrl.bitrate.show_link" controls autoplay></audio>    
   定义获取当前时间的函数  
   _currenTime(){  
   this.currenTime = this.$refs.player.currentTime
}  
   定义获取总播放时间的函数
  _duration(){  
   this.durationTime = this.$refs.player.duration
} 
   设置绑定监听函数  
   addEventListener(){
   this.$refs.player.addEventListener('timeupdata',_currenTime);  
   this.$refs.player.addEventListener('canplay',_currenTime);   
}
   设置解除绑定的监听函数
   removeEventListeners(){  
    this.$refs.player.removeEventListener('timeupdata',_currenTime);  
   this.$refs.player.remove EventListener('canplay',_currenTime);   
}  
   然后在mounted中调用 addEventListener()  
   在beforeDestroyed中调用  removeEventListeners（）  
 以上这么做是为了获取到两个属性 当前播放的时间 currenTime 和 总播放时间durationtime  
 这两个属性将绑定到组件LRC上 再在LRC中的props直接获取  
 5.歌词格式的处理 获取到的歌词内容并不是我们想显示的那个样子  
   所以要做一些格式化的操作  
   在data中定义lyric存放歌词数据  
   通过以下方法进行匹配 
   歌词本来是这样的 "[00:03:32.71]今天我 "
 var lyrics = res.data.lrcContent.split("\n");  //去掉换行符  
  var lrcObj = {}; 
 for(var i = 0 ;i<lyrics.length;i++){  
   var lyric = decodeURIComponent(lyrics[i]);  
   var timeReg = /\[\d*:\d*((\.|\:)\d*)*\]/g;  
   var timeRegExpArr = lyric.match(timeReg);  
   if(!timeRegExpArr)continue;  
//把前面的歌词的时间部分 替换成空数组 拿到歌词的正文  
   var clause = lyric.replace(timeReg,'');  
  
      for(var k = 0,h = timeRegExpArr.length;k < h;k++) {  
          var t = timeRegExpArr[k];  
          var min = Number(String(t.match(/\[\d*/i)).slice(1)),  
              sec = Number(String(t.match(/\:\d*/i)).slice(1));  
       // console.log（min+‘--’+sec）  0--12 
          var time = min * 60 + sec;  
          lrcObj[time] = clause;  
   经过以上处理 歌词就会变成是：     
   ![images](https://github.com/eamoney/music/blob/master/images/lrc.png)  
6.歌词的适配  
   这里定义了一个计算属性 getAllkey 来拿到上面lrcObj的键
    getAllKey(){    
  for(var i in this.lrcData){   
    this.keyArr.push(i);   
  }   
}   
   特别注意：拿到的键可能是 ：  
 117 118  121 这么不连续的值 我们要实现的功能是想让对应的歌词显示红色  
   因此我们定义了一个class  
   那怎么去应用呢？  
   获取刚才第4步 从父组件传过来的时间
   只要当前时间是在这个区间 那么我们就为这句歌词对应的p标签加上样式  
   :class="{active:parseInt(currentTime) >= keyArr[index] && parseInt(currentTime) < keyArr[index+1]}"  

![images](https://github.com/eamoney/music/blob/master/images/lrc1.png)  
7.歌词的滚动   
  定义了一个srcollLrc方法    
    srcollLrc(key,index){  
    const lrcDiv = this.$refs.lrc  
     if(key < this.currentTime && key > this.currentTime - (this.keyArr  [index+1] - this.keyArr[index])){  
         lrcDiv.style.top = -((index-2)*30)+"px"  
     }  
     console.log(key+'---'+index)  
  }  
}  
实现歌词的滚动  --调试  
![images](https://github.com/eamoney/music/blob/master/images/2.png)  
   
七.歌手列表的实现  
![images](https://github.com/eamoney/music/blob/master/images/singer.png)   
  1.这边歌手的数据是写死的 规定了一个数组  数组里面存放每个歌手对应的id 通过id  
    获取歌手信息  
    artistsArr:["2517","7994","1091","45561","2507","245815","1077","1204","1117","82366"],  
   2.在mounted中使用axios请求时 是用for循环的  
   for(var i =0;i<this.artistsArr.length;i++){  
   //循环遍历每个歌手对应id 拿到数据后放入artistsData中  
  const artistsURL = this.HOST + "/v1/restserver/ting?  method=baidu.ting.artist.getInfo&tinguid="+ this.artistsArr[i];  
  this.$axios.get(artistsURL)  
    .then(res => {  
 //push进一个对象 包含歌手的以下信息  
      this.artistsData.push({  
        avatar_s500:res.data.avatar_s500,  
        name:res.data.name,  
        ting_uid:res.data.ting_uid  
      })  
    })  
    .catch(error => {  
      console.log(error);  
    })  
}  
  3.通过得到的artisisData使用v-for渲染到页面上  
  4.改造每个li 为router-link 跳转到歌手详情页面  
  
![images](https://github.com/eamoney/music/blob/master/images/singer2.png)   
    router-link tag="li" :to="{name:'ArtistsDetails',params:{avatar:item.avatar_s500,name:item.name,ting_uid:item.ting_uid}}" 
             :key="index" class="artist" v-for="(item,index) in artistsData">  
    跳转的同时 传入parmas参数  
  5.新建两个子组件 list显示歌曲列表 title显示歌手信息 ArtistsDetails通过
      this.$route拿到父组件中传来的参数 并且通过属性绑定的方式传给自己的子组件   list  <Title :name="this.$route.params.name" :avatar="this.$route.params.avatar"/>  
<List :ting_uid="this.$route.params.ting_uid"/>  

  6.通过props接收父组件传过来的值 通过这个值就能用axios发起请求拿到对应歌手
    的歌曲列表信息  
    改造歌曲列表（list）中的li 使其能跳转到播放页 router-link传入刚遍历的item.song_id   
  
八.搜索列表的改造   

![images](https://github.com/eamoney/music/blob/master/images/search.png)   
![images](https://github.com/eamoney/music/blob/master/images/search2.png)   
   分析:当输入歌手名字后 点击搜索按钮 列表会渲染符合输入搜索框的名字并向服务器发起请求 拿到数据 渲染到页面  
   1.我们需要知道收索框的值  
     这边我可以用e.target.vaule获取 但我使用了另一种更加简单的方式  
     v-model  
     <input type="text" name="" placeholder="输入搜索内容" v-model="searchContent">  
      通过v-model实现数据双向绑定 当输入框的值改变之后 对应在实例上的data中的
     searchContent也会发生改变  
  2.为收索按钮注册点击事件 
    <button type="button" @click="searchHandler" name="button">搜索</button>  
     也可以为input注册input事件 当输入框值改变后 自动调用searchHandle方法  
    <input type="text" name="" placeholder="输入搜索内容" v-model="searchContent" @input=" searchHandler">  
     
      searchHandler(){  
  const searchURL = this.HOST + "/v1/restserver/ting?  method=baidu.ting.search.catalogSug&query="+this.searchContent;  
    this.$axios.get(searchURL)  
    .then(res => {  
      if(res.data.error_code == 22001){  
        alert('搜索数据不存在');  
        return ;  
      }  
      this.songlist = res.data.song  
    })   
    .catch(error => {  
      console.log(error);  
    })  
}  
九.细节优化 - 下拉刷新  
![images](https://github.com/eamoney/music/blob/master/images/refresh.png)   

![images](https://github.com/eamoney/music/blob/master/images/refresh2.png)   
  1.在morelist中下拉刷新会触发相应的事件 从新定义请求地址url中的offset参数  
    拿到新的数据  】
  2.使用的插件是vue-pull-refresh  
    先安装包  
    然后在morelist中引入  import VuePullRefresh from 'vue-pull-refresh';  
     挂载插件到页面上  <VuePullRefresh :on-refresh="onRefresh">  
     当下拉刷新时会触发函数  返回一个Promise对象  
    然后执行判断  
    that.offset >= res.data.billboard.billboard_songnum - 12 ? console.log("没数据了") :    that.offset += 12,  
      这样每次刷新都会使offet加12 直到没数据为止
  
--2019 07 
