# Js手势滑动事件（判断上下左右方向）



#### 项目里有个上拉加载数据的需求，就造了一个上拉加载下拉刷新的轮子，当然这里只说手势滑动事件，滑动到最顶和最底部动画，参考了swiper.js

```
  <template>
      <div id="pullScrollCell">
    <div id="touch">
        <div>内容</div>
    </div>
  </div>
  </template>
  
  <script>
  data() {
    return {
      startX: '',
      startY: '',
      limitY: 0,
    }
  }
  mounted() {
      document.querySelector('#pullScrollCell').addEventListener('scroll', this.debounce(this.pullUpReload, 600));  //设置防抖防止重复触发
      document.querySelector('#touch').addEventListener('touchstart', this.touchstart); //收拾开始
      document.querySelector('#touch').addEventListener('touchmove', this.touchmove); //手势移动
      document.querySelector('#touch').addEventListener('touchend', this.touchend); //手势结束
    }
    methods: {
  //防抖
  debounce(fn, delay) {
    let timer = null
    return () => {
    if (timer) {
      clearTimeout(timer) 
    }
    timer = setTimeout(fn, delay)
  } 
  },
  //手势开始
  touchstart(e) {
    // e.preventDefault() //阻止默认事件
    this.startX=e.changedTouches[0].pageX;
    this.startY=e.changedTouches[0].pageY;
  },
  
    /*
  手势移动中
  touchmove的最后坐标减去touchstart的起始坐标，
  X的结果如果正数，则说明手指是从左往右划动；X的结果如果负数，
  则说明手指是从右往左划动；Y的结果如果正数，则说明手指是从上往下划动；Y的结果如果负数，
  则说明手指是从下往上划动。
    */
  touchmove(e) {
    // e.preventDefault() //阻止默认事件
    letscrollTop=Math.ceil(document.querySelector('#pullScrollCell').scrollTop);
    letmoveEndX=e.changedTouches[0].pageX;
    letmoveEndY=e.changedTouches[0].pageY;
    letX=moveEndX-this.startX;
    letY=moveEndY-this.startY;
    //向右
    if (Math.abs(X) >Math.abs(Y) &&X>0) {
    // console.log('向左');
    }
    //向左
    if (Math.abs(X) >Math.abs(Y) &&X<0) {
    // console.log('向右');
    }
    //向上
    if (Math.abs(Y) >Math.abs(X) &&Y>0) {
    // console.log('向上');
    if (scrollTop==0) {
    e.preventDefault() //阻止默认事件(微信上拉刷新会触发微信默认事件，导致不能调用刷新加载)
    }
    this.limitY=Math.min(Y, 100) //向上滑动动画最大100
    document.querySelector('#touch').style = `transition: all 0ms ease 0s; transform: translate3d(0px, ${this.limitY}px, 0px)`;

  }
  //向下
  if (Math.abs(Y) >Math.abs(X) &&Y<0) {
      // console.log('向下');
      this.limitY=Math.max(Y, -100) //向下滑动动画最大-100
      document.querySelector('#touch').style = `transition: all 0ms ease 0s; transform: translate3d(0px, ${this.limitY}px, 0px)`;
  }
  },
  //手势结束
  touchend(e) {
    letscrollTop=Math.ceil(document.querySelector('#pullScrollCell').scrollTop); //滚动条到顶部的位置
    document.querySelector('#touch').style = `transition: all 300ms ease 0s; transform: translate3d(0px, 0px, 0px)`;
    if (scrollTop==0&&this.limitY>=60) {
        滚动条在顶部并且下拉值大于60，触发
      }
    },
  }
<script>
```
