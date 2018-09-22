<template>
  <div id="app">
    {{h}}
    <transition name="a">
      <button v-if="h <= 0" @click="starttime" >Start Time</button>
      <button v-if="h >= 100" @click="dtime">Down Time</button>
    </transition>
    <TheBackground :h="h"></TheBackground>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import TheBackground from './components/TheBackground.vue'

export default {
  name: 'app',
  data(){
    return{
      h: 0,
      time_id: 0,
      d_time: 1500,
      down_time: 300,
    }
  },
  methods:{
    starttime(){
      if(this.time_id){
        clearInterval(this.time_id)
        this.time_id = 0
      }
      this.time_id = setInterval(()=>{
        if(this.h >= 100 ){
          clearInterval(this.time_id)
          this.time_id = 0;
          this.notifyMe("take break ,go workaround!!!!!!!!!!")
          return false
        }
        this.h += this.add_time
      },1000);
    },
    test:function(){
      alert('test');
    },
    dtime(){
      if(this.time_id){
        clearInterval(this.time_id)
        this.time_id = 0
      }
      this.time_id = setInterval(()=>{
        if(this.h <= 0 ){
          clearInterval(this.time_id)
          this.time_id = 0;
          this.notifyMe("GO WORK!!!!!!!!!!!!cheers!!!!")
          return false
        }
        this.h -= this.delete_time
      },1000);
    },
    test:function(){
      alert('test');
    },
    notifyMe(msg) {
  // 首先讓我們確定瀏覽器支援 Notification
  if (!("Notification" in window)) {
    alert("這個瀏覽器不支援 Notification");
  }

  // 再檢查使用者是否已經授權執行 Notification
  else if (Notification.permission === "granted") {
    // 如果已經授權就可以直接新增 Notification 了!
    var notification = new Notification(msg);
  }

  // 否則，我們會需要詢問使用者是否開放權限
  else if (Notification.permission !== 'denied') {
    Notification.requestPermission(function (permission) {
      // 如果使用者同意了就來新增一個 Notification 打聲招呼吧
      if (permission === "granted") {
        var notification = new Notification(msg);
      }
    });
  }

  // 如果使用者還是不同意授權執行 Notification
  // 最好還是進行適當的處理以避免繼續打擾使用者
}
  },
  computed:{
    add_time(){
      return  1 / this.d_time * 100 
    },
    delete_time(){
      return 1 / this.down_time * 100
    }
  },
  components: {
    HelloWorld,
    TheBackground
  }
}
</script>

<style>
html,body{
  margin:0px;
  padding:0px;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
