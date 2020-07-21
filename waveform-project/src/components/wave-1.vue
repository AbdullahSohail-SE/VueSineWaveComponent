<template>
  <div id="canvasDiv" style="position: relative;">
    <canvas ref="waveLayer" id="waveLayer" style="position: absolute; top: 0;left: 0;"></canvas>
    <canvas ref="ballLayer" id="ballLayer" style="position: absolute; top: 0;left: 0;"></canvas>
  </div>
</template>
<script>

export default {
  data(){
    return {
      waveLayer:"",
      ballLayer:"",
      waveStartingPositionY:0,
      waveStartingPositionX:0,
      lastPosX:0,
      ballContext:"",
      timeElapsed:0,
      increment:0,
      xAxisPointsGap:3,
      yAxisPointsGap:2,
      xAxisScale:10,
      yAxisScale:1,
      intervalKey:0,
      fps:30
    }
  },
  props:{
    amplitude:{
      type:Number,
      required:true
    },
    frequency:{
      type:Number,
      required:true
    }
  },
  methods:{
    initializeData(){
      this.waveLayer=this.$refs.waveLayer;
      this.ballLayer=this.$refs.ballLayer;
      this.waveLayer.width=window.innerWidth;
      this.waveLayer.height=window.innerHeight;
      this.ballLayer.width=window.innerWidth;
      this.ballLayer.height=window.innerHeight;
      this.waveStartingPositionY=waveLayer.height/2;
      this.lastPosX=waveLayer.width;
      this.waveStartingPositionX=0;
      this.ballContext=this.ballLayer.getContext('2d');
      this.ballContext.lineTo(this.waveStartingPositionX,this.waveStartingPositionY);

    },
    renderCanvas(){
     this.renderWave();

     this.intervalKey=setInterval(() => {
       this.renderBall();
     }, 1000/this.fps);
     
    },
    renderBall(){
      
       var marginLeft=30;
       var Y=this.amplitude*Math.sin((this.timeElapsed)*this.frequency/60);
       if(Y>=0)
            Y=-Y;
       var d=this.ballContext;
      
       d.clearRect(0,0,innerWidth,innerHeight);
       d.fillStyle = "purple"; //blue
       d.beginPath();
       
       var Y=(this.amplitude*Math.sin(2*Math.PI * this.frequency * this.timeElapsed)-this.amplitude);
       d.arc((this.timeElapsed*this.xAxisScale)+marginLeft,this.waveStartingPositionY+(Y*this.yAxisScale),5,0,Math.PI*2,false);
      
       d.closePath();
       d.fill();
      
       this.timeElapsed+=1/this.fps;
       var that=this;

      // setTimeout(() => {
      //   requestAnimationFrame(that.renderBall);
      // }, 1000/this.fps);
     
    },
    renderWave(){
      var waveContext=this.waveLayer.getContext('2d');

      var marginTop=20;
      var marginLeft=30;

      waveContext.lineCap="round";
      waveContext.lineJoin="round";

      waveContext.beginPath();
      waveContext.lineWidth=20;
      waveContext.moveTo((this.waveStartingPositionX+marginLeft-10),(marginTop+this.waveStartingPositionY));
      waveContext.lineTo((window.innerWidth),(marginTop+ this.waveStartingPositionY));
      waveContext.strokeStyle="#cebfd8"
      waveContext.stroke();
      waveContext.closePath();

      waveContext.beginPath();
      waveContext.lineWidth=2;
      waveContext.moveTo(this.waveStartingPositionX+marginLeft-10,this.amplitude/2);
      waveContext.lineTo(this.waveStartingPositionX+marginLeft-10,this.waveStartingPositionY);
      waveContext.strokeStyle="#cebfd8"
      waveContext.stroke();
      waveContext.closePath();

      waveContext.textAlign = 'center';
      waveContext. textBaseline = 'middle';
      
      
      waveContext.beginPath();
      waveContext.strokeStyle="#cebfd8";
      waveContext.lineWidth=5;

      var YPoints=[];
      
      for(var i=0;i<window.innerWidth;i++){

          var Y=(this.amplitude*Math.sin(2*Math.PI * this.frequency * i))-this.amplitude;
          waveContext.lineTo(marginLeft+(i*this.xAxisScale),this.waveStartingPositionY + (Y*this.yAxisScale));

          // if(i==0)
          // waveContext.textAlign = 'left';
          // else
          // waveContext.textAlign = "center";

          if(Y%this.yAxisPointsGap==0 && Y!=0){
            if(YPoints.indexOf(Y)==-1)
            YPoints.push(Y);
          }

          if(i%this.xAxisPointsGap==0)
          {
            waveContext.fillText(i,marginLeft+(i*this.xAxisScale),marginTop+ this.waveStartingPositionY);
          }
      }
     

      YPoints.forEach((val,index)=>{
        waveContext.fillText(val*-1,7,this.waveStartingPositionY+(val*this.yAxisScale));
      })
      
      waveContext.stroke();
      waveContext.closePath();
    }
  },
  mounted(){
    this.initializeData();
    this.renderCanvas();
  },
  beforeDestroy(){
    clearInterval(this.intervalKey);
  }
}
</script>
<style scoped>

</style>