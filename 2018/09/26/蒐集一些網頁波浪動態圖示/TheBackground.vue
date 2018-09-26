<template>
    <!-- <div class="bg" :style="{height: h + 'vh'}">
        {{ h }}
    </div> -->
    <div id="wrapper">
	    <div id="balls" :style="{top: realH}">
	      <div id="ball1"></div>
	      <div id="ball2"></div>
	      <div id="ball3"></div>
	    </div>
	</div>
</template>
<style scoped>
/* .bg{
    position: fixed;
    bottom: 0px;
    width: 100vw;
    height: 0px;
    z-index: -1;
    background: greenyellow;
    border: 1px solid red;
    transition: all 1s;
} */
#wrapper{
			  position: fixed;
              z-index: -1;
              top: 0px;
			  height: 100vh;
			  width: 100vw;
			  border: greenyellow 1px solid;
			  overflow: hidden;

			}
			#balls{
			  position: relative;
			  height:100%;
			  width: 100%;
			  top: 50%;
              transition: all 1s;
			}
			#ball1,#ball2,#ball3{
			  position: absolute;
			  right: 0;
			  margin-left: -750%;
			  height: 1500%;
			  width: 1500%;
			  border-radius: 50%;
			  left: 50%;
			  background-color: greenyellow;
			}
			#ball1{
			  animation: move 5s 0s infinite ease-in-out;	  
			}
			#ball2{
       animation: move 5s 2s infinite ease-in-out;    
			}
			#ball3{
			  animation: move 5s 4s infinite ease-in-out;
			}
			@keyframes move{
				from{transform: translateX(-10%);}
				to{transform: translateX(10%);}
			}
</style>
<script>
export default {
    props:['h'],
    data(){
        return {
        }
    },
    computed:{
        realH(){
            return (100 - this.h - ( this.h < 50 ? 5 : 0) ) + '%'
        }
    }
}
</script>
