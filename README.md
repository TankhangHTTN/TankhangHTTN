<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>场景相机</title>
  <style>
    @import url("https://fonts.cdnfonts.com/css/transponder-aoe");html,body{height:100%;-webkit-user-select:none;-moz-user-select:none;-ms-user-select:none;user-select:none}body{display:flex;justify-content:center;background:rgb(194 206 219);background:linear-gradient(205deg,rgb(238 233 245) 0%,rgb(30 34 42) 100%);margin:0;font-family:"poppins",sans-serif}.footer{position:absolute;bottom:0;right:0;font-family:monospace;color:#aaa;padding:10px;font-size:12px}#noise-svg{height:100%;width:100%;opacity:0.1}#noise-rect{width:100vw;height:100vh}.outer-rim,.outer-rim *{position:absolute;top:0;bottom:0;left:0;right:0;margin:auto;background:linear-gradient(205deg,rgb(69 75 85) 0%,rgb(22 25 32) 100%)}.outer-rim{--init-size:min(min(712px,90vw),90vh);width:var(--init-size);height:var(--init-size);border-radius:50%;overflow:hidden;box-shadow:-40px 40px 30px 10px rgb(0,0,0,0.4),-80px 80px 40px 10px rgb(0,0,0,0.6),-120px 120px 80px 10px rgb(0,0,0,0.6)}.outer-rim-2{width:calc(var(--init-size) * 0.97);height:calc(var(--init-size) * 0.97);box-shadow:2px -2px 2px 0 rgb(108,115,129,0.8),-30px 30px 12px 10px rgb(0,0,0,0.5),inset -2px 2px 2px 0 rgb(108,115,129,0.2),inset 2px -2px 2px 0px rgb(0,0,0,0.3);border-radius:50%}.inner-rim{border-radius:50%;width:calc(var(--init-size) * 0.92);height:calc(var(--init-size) * 0.92);box-shadow:-2px 2px 2px 0 rgba(108,115,129,0.4),2px -2px 1px 0px rgba(0,0,0,0.2),inset -3px 3px 2px 1px rgba(0,0,0,0.5)}.outer-window.invert{rotate:180deg;transform-origin:50% 88%;scale:0.99}.outer-window{background:none;width:calc(var(--init-size) * 0.83);height:calc(var(--init-size) * 0.83 / 1.7);overflow:hidden;top:4%;bottom:unset}.window{top:-1%;background:none;width:calc(var(--init-size) * 0.83);height:calc(var(--init-size) * 0.83 / 2);bottom:unset;border-radius:12px;overflow:hidden;box-shadow:-2px 2px 2px 0 rgba(108,115,129,0.4),2px -2px 1px 0px rgba(0,0,0,0.2)}.outer-window:not(.invert) svg{opacity:0;transition:opacity 0.6s ease-in-out}.power-on .outer-window:not(.invert) svg{opacity:unset}.window-rounded svg#mountains{bottom:unset;width:100%;left:0%;filter:contrast(1) grayscale(0.6) brightness(1)}.window-rounded svg#mountains #clouds,.window-rounded svg#mountains #mid-clouds{opacity:0;translate:0 10%}.window-rounded svg#mountains #strike-1,.window-rounded svg#mountains #strike-2,.window-rounded svg#mountains #back-clouds-1,.window-rounded svg#mountains #back-clouds-2,.window-rounded svg#mountains #back-clouds-3{opacity:0;translate:0 -4%}.window-rounded svg#mountains #moon{translate:0 60%}.window-rounded svg#mountains #snow-1,.window-rounded svg#mountains #snow-2{opacity:0;scale:1.5}.window-rounded svg#mountains #stars{opacity:0;translate:0 30%}.window-rounded svg#mountains #sky{fill:#9fcfeb}.power-on .window-rounded svg#mountains,.power-on .window-rounded svg#mountains *{transition:all 2s ease-in-out !important}
  </style>
</head>

<body>
  <svg id="noise-svg">
    <filter id='noiseFilter'>
      <feTurbulence type='fractalNoise' baseFrequency='0.5' numOctaves='3' stitchTiles='stitch' />
    </filter>
    <rect id="noise-rect" filter='url(#noiseFilter)' />
  </svg>

  <div class="outer-rim">
    <div class="outer-rim-2">
      <div class="inner-rim">
        <div class="outer-window"></div>
      </div>
      <div class="outer-window invert">
        <div class="window">
          <div class="window-rounded">
            <div class="loading-bar">
              <div></div>
              <div></div>
              <div></div>
              <div></div>
            </div>
            <div class="back-circle"></div>
            <div class="circle" id="rot">
            </div>
            <div class="left-modal">
              <div class="power" onclick="document.querySelector('.outer-rim').classList.toggle('power-on');">
                <div class="inner-power">
                </div>
              </div>
            </div>
            <div class="right-modal">
              <p class="label">Temperature</p>
              <div class="screen">
                <p class="temp">
                  34°F
                </p>
              </div>
              <div class="toggle active" id="toggle-far">°F</div>
              <div class="toggle two" id="toggle-cel">°C</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  </div>
  <script>
    var rotateDiv=document.getElementById('rot');var rotateIcons=document.getElementById('rot-icons');var clickRotateDiv=document.getElementById('click-rot');var angle=0;clickRotateDiv.onclick=function(){angle+=60;rotateDiv.style.transform='rotate('+angle+'deg)';rotateIcons.style.transform='rotate('+angle+'deg)'};var step=2;var color1='rgba(0,0,0,0.5)';var color2='rgba(0,0,0,0.1)';var gradient=' conic-gradient(';for(var i=0;i<360;i+=step){var color=i%(2*step)===0?color1:color2;gradient+=color+' '+i+'deg, '}gradient=gradient.slice(0,-2)+'), rgb(85 93 108)';rotateDiv.style.background=gradient;var toggles=document.querySelectorAll('.toggle');var tempElement=document.querySelector('.temp');let isAnimating=false;toggles.forEach(function(toggle){toggle.addEventListener('click',function(){if(this.classList.contains('active')||isAnimating){return}toggles.forEach(function(toggle){toggle.classList.remove('active')});this.classList.add('active');var tempValue=parseFloat(tempElement.textContent);if(this.id==='toggle-cel'){var celsius=Math.round((tempValue-32)*5/9);tempElement.textContent=celsius+'°C'}else if(this.id==='toggle-far'){var fahrenheit=Math.round(tempValue*9/5+32);tempElement.textContent=fahrenheit+'°F'}})});let currentTempF=34;function easeInOutCubic(t){return t<.5?4*t*t*t:(t-1)*(2*t-2)*(2*t-2)+1}function changeTemp(element,newTemp){let unit=element.innerHTML.includes("F")?"°F":"°C";let currentTemp=unit==="°F"?currentTempF:Math.round((currentTempF-32)*5/9);let finalTemp=unit==="°F"?newTemp:Math.round((newTemp-32)*5/9);let duration=2000;let startTime=null;function animate(currentTime){if(startTime===null){startTime=currentTime}let elapsed=currentTime-startTime;let progress=Math.min(elapsed/duration,1);progress=easeInOutCubic(progress);let tempNow=Math.round(currentTemp+(progress*(finalTemp-currentTemp)));element.innerHTML=`${tempNow}${unit}`;if(progress<1){requestAnimationFrame(animate)}else{currentTempF=newTemp;isAnimating=false}}isAnimating=true;requestAnimationFrame(animate)}window.onload=function(){const sixths=Array.from(document.querySelectorAll('.sixths'));let index=0;let temp=document.querySelector('.temp');document.querySelector('#rot-icons').addEventListener('click',()=>{sixths[index].classList.remove('active');index=(index+1)%sixths.length;sixths[index].classList.add('active');if(index==0){changeTemp(temp,34);console.log("sun")document.querySelector('#mountains').classList.remove("snow");document.querySelector('#mountains').classList.remove("clouds")}else if(index==1){changeTemp(temp,27);console.log("sunset")document.querySelector('#mountains').classList.add("sunset")}else if(index==2){changeTemp(temp,14);console.log("moon")document.querySelector('#mountains').classList.remove("sunset");document.querySelector('#mountains').classList.add("moon")}else if(index==3){changeTemp(temp,16);console.log("clouds")document.querySelector('#mountains').classList.add("clouds")}else if(index==4){changeTemp(temp,8);console.log("storm")document.querySelector('#mountains').classList.add("storm")}else if(index==5){changeTemp(temp,-4);console.log("snow")document.querySelector('#mountains').classList.remove("moon");document.querySelector('#mountains').classList.remove("storm");document.querySelector('#mountains').classList.add("snow")}let loadingBar=document.querySelector('.loading-bar');loadingBar.classList.add('active');setTimeout(()=>{loadingBar.classList.remove('active')},1200)})};
  </script>
</body>

</html>
