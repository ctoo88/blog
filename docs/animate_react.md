## Animate.css应用之react

2018/01/07

### 关键字

>[Animate.css](https://daneden.github.io/animate.css/)

### 概述

通常我们不推荐在react中操作原生DOM，那么采用CSS动画就是很好的一个选择（CSS动画的性能一直以来都比较令人满意）。而animate.css 是一个来自国外的 CSS3 动画库，它预设了抖动（shake）、闪烁（flash）、弹跳（bounce）、翻转（flip）、旋转（rotateIn/rotateOut）、淡入淡出（fadeIn/fadeOut）等多达 60 多种动画效果，几乎包含了所有常见的动画效果。本文就是介绍如何通过改变react组件state，动态更新className来实现动画。

### 准备工作

安装：

    $ npm install animate.css --save

引用：

    <link rel="stylesheet" href="animate.min.css">

### 实现

使用方法：

在需要实现动画的元素上添加class”animated“，如果需要循环播放就添加”infinite“，还可以修改css样式来自定义动画持续时间、延迟、循环次数。

    #my_element {
      animation-duration: 3s;
      animation-delay: 2s;
      animation-iteration-count: infinite;
    }

目前支持的动画类型：

| Class Name | | | | 
|--------------------|--------------------|--------------------|--------------------|
| `bounce` |`flash` |`pulse` |`rubberBand` |
| `shake` |`headShake` |`swing` |`tada` |
| `wobble` |`jello` |`bounceIn` |`bounceInDown` |
| `bounceInLeft` |`bounceInRight` |`bounceInUp` |`bounceOut` |
| `bounceOutDown` |`bounceOutLeft` |`bounceOutRight` |`bounceOutUp` |
| `fadeIn` |`fadeInDown` |`fadeInDownBig` |`fadeInLeft` |
| `fadeInLeftBig` |`fadeInRight` |`fadeInRightBig` |`fadeInUp` |
| `fadeInUpBig` |`fadeOut` |`fadeOutDown` |`fadeOutDownBig` |
| `fadeOutLeft` |`fadeOutLeftBig` |`fadeOutRight` |`fadeOutRightBig` |
| `fadeOutUp` |`fadeOutUpBig` |`flipInX` |`flipInY` |
| `flipOutX` |`flipOutY` |`lightSpeedIn` |`lightSpeedOut` |
| `rotateIn` |`rotateInDownLeft` |`rotateInDownRight` |`rotateInUpLeft` |
| `rotateInUpRight` |`rotateOut` |`rotateOutDownLeft` |`rotateOutDownRight` |
| `rotateOutUpLeft` |`rotateOutUpRight` |`hinge` |`jackInTheBox` |
| `rollIn` |`rollOut` |`zoomIn` |`zoomInDown` |
| `zoomInLeft` |`zoomInRight` |`zoomInUp` |`zoomOut` |
| `zoomOutDown` |`zoomOutLeft` |`zoomOutRight` |`zoomOutUp` |
| `slideInDown` |`slideInLeft` |`slideInRight` |`slideInUp` |
| `slideOutDown` |`slideOutLeft` |`slideOutRight` |`slideOutUp` |

按钮水波纹：

    export default class Button extends React.Component {
      constructor() {
        super();
        this.state = {
          touch: false
        }
      }

      render() {
        let {touch} = this.state;
        
        return (
          <span className="add" onTouchStart={this.touchstart.bind(this)} onClick={this.props.onClick}>
            <i className={'animated ' + (touch ? 'zoomIn' : '')}></i>
            {this.props.children}
          </span>
        )
      }

      touchstart() {
        this.setState({touch: true});
        setTimeout(() => {
          this.setState({touch: false});
        }, 500);
      }
    }

刷新列表动效：

    export default class List extends React.Component {
      constructor() {
        super();
      }

      render() {
        let {data} = this.props;

        return (
          <div>
            <ul>
            {
              data.map(item =>
                <li key={item} className={'animated fadeInRight ' + 'item' + item}>list item {item}</li>
              )
            }
            </ul>
          </div>
        )
      }
    }

css样式：

    .item2 {
      animation-delay: .1s;
    }
    .item3 {
      animation-delay: .2s;
    }
    .item4 {
      animation-delay: .3s;
    }
    .item5 {
      animation-delay: .4s;
    }
    .item6 {
      animation-delay: .5s;
    }
    .item7 {
      animation-delay: .6s;
    }
    .item8 {
      animation-delay: .7s;
    }
    .item9 {
      animation-delay: .8s;
    }

### 最后

虽然借助animate.css能够很方便、快速的制作CSS3动画效果，但还是建议看看animate.css的源码，理解其中原理。
