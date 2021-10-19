---
title: 项目总结
date: 2021-10-15 15:29:21
tags:
  - echarts
  - vue
  - hammer
  - hexo
categories:
  - 项目总结
---

## 手机端地图缩放和饿了么 tab 切换组件左右滑动切换会相互影响，因此引入 hammer.js 做手势控制

```
// 控制手势滑动切换距离
bindEvents() {
  let that = this;
  let hammerTest = new Hammer(document.querySelector("body"));
  hammerTest.on("swipeleft swiperight", function (ev) {
    if (ev.distance > 100) {
      switch (ev.type) {
        case "swipeleft":
          that.active++;
          break;
        case "swiperight":
          that.active--;
          break;
        default:
          break;
      }
    }
  });
    },
```

## echarts 中纵坐标有负值如何倒圆角

series-bar 的 data 项可以针对具体项做操作，所以这里的思路就是对原始数据遍历，对每个数据都做个性化处理，以此解决倒圆角的问题
可参考[echarts-柱状图实现正负坐标倒圆角设置及 bar 颜色设置](https://blog.csdn.net/Y_shihao/article/details/110558663)

```
series: [
  {
    type: "bar",
    showBackground: true,
    barWidth: 10,
    // 循环数组，为一个bar配置单个属性，处理负值的倒角问题
    data: arr.map((item) => {
        return {
          value: item,
          label: {
            normal: {
              show: false,
              position: "outside",
              fontSize: 12,
            },
          },
          itemStyle: {
            normal: {
              barBorderRadius: item > 0 ? [6, 6, 0, 0] : [0, 0, 6, 6],
              color: new echarts.graphic.LinearGradient(0, 1, 0, 0, [
                { offset: 0, color: _this[_this.type].x1_1 },
                { offset: 1, color: _this[_this.type].x1_2 },
              ]),
            },
          },
        };
      }),
  },
]
```

## 模拟支付宝查看基金可左滑查看更多项，也可以下滑

整体布局分为左右两块，左侧和右侧均分为上下，左侧和右侧上面绝对定位固定，布局代码如下

```
<div class="jj-table-wrap">
  <div class="jj-table-mask" v-if="scrollLeft > 0"></div>
  <div class="jj-table-left">
    <div class="jj-table-left-top">产品名称</div>
    <ul class="jj-table-left-bottom">
      <li>
        ...
      </li>
      ...
    </ul>
  </div>
  <div class="jj-table-right">
    <ul class="jj-table-right-top">
      <li style="width: 120px">数据更新日期</li>
      <li style="width: 120px">产品成立日期</li>
    </ul>
    <ul class="jj-table-right-bottom">
      <li
      >
        <div class="cell" style="width: 120px">
          ...
        </div>
        <div class="cell" style="width: 120px">
          ...
        </div>
      </li>
    </ul>
  </div>
</div>

.jj-table-wrap {
    height: calc(100vh - 66px - 46px);
    font-size: 12px;
    overflow-x: hidden;
    overflow-y: auto;
    background: #fff;
    .jj-table-mask {
      position: absolute;
      width: 190px;
      height: 100%;
      box-shadow: 0 3px 10px rgb(0 0 0 / 12%);
      z-index: 99;
    }
    .jj-table-left {
      float: left;
      width: 190px;

      .jj-table-left-top {
        width: 190px;
        position: absolute;
        height: 42px;
        color: #666666;
        padding-left: 14px;
        line-height: 42px;
        background: #fff;
        border-bottom: 1px solid #ebedf0;
        z-index: 20;
        box-sizing: border-box;
      }
      .jj-table-left-bottom {
        margin-top: 42px;

      }
    }
    .jj-table-right {
      float: left;
      width: calc(100vw - 190px);
      overflow-x: auto;
      overflow-y: hidden;
      .jj-table-right-top {
        width: calc(100vw - 190px);
        position: absolute;
        display: flex;
        background: #fff;
        border-bottom: 1px solid #ebedf0;
        height: 42px;
        overflow-y: auto;
        box-sizing: border-box;
        li {
          flex: 0 0 auto;
          float: left;
          color: #666666;
          padding-left: 14px;
          line-height: 42px;
          width: 100px;
          box-sizing: border-box;
        }
      }
      .jj-table-right-bottom {
        width: 900px;
        margin-top: 42px;
        li {
          height: 67px;
          display: flex;
          .cell {
            flex: 0 0 auto;
            font-size: 14px;
            color: #242424;
            font-weight: 600;
            padding: 14px 0 10px 14px;
            box-sizing: border-box;
            width: 100px;
            background: #fff;
            border-bottom: 1px solid #eeeeee;
          }
        }
      }
    }
  }

```

关键点在于向左侧滑动的时候，怎么联动右侧上面的标题，思路是监听右侧下面滑动事件，在右侧下面滑动的时候，手动让右侧上方的的标题栏同步滑动，代码实现如下

```
//横向滚动监听
let rowScrollDom = document.querySelector(".jj-table-right"),
  targetScroolXDom = document.querySelector(".jj-table-right-top");

rowScrollDom.addEventListener("scroll", (e) => {
  this.scrollLeft = e.srcElement.scrollLeft;
  targetScroolXDom.scrollTo(e.srcElement.scrollLeft, 0);
});

targetScroolXDom.addEventListener("scroll", (e) => {
  rowScrollDom.scrollTo(e.srcElement.scrollLeft, 0);
});

```

## 搜索历史参照京东交互

获取当前搜索历史列表下的所有历史节点，遍历循环获取其距离左侧视口的距离，从而判断属于第几行，然后进行截断操作

```
//历史记录 页面超出三行截取数据
let count = 0;
setTimeout(() => {
  let ulChid = document.querySelector(".history-list").childNodes; //获取父容器所有子节点
  ulChid.forEach((i, index) => {
    console.log(i + "：", i.offsetLeft);
    if (i.offsetLeft === 13) {
      count++;
      if (count === 4) { //超过三行
        this.idx = index;
        this.hasMoreBtn = true;
      }
    }
  });
  // 通过超出三行元素的下标去截断
  if (this.idx > 0) {
    this.historySearchShow = this.historySearch.slice(0, this.idx);
  } else {
    this.historySearchShow = this.historySearch;
  }
});
```

## vue 后台管理项目用户动态菜单权限

```
//利用路由守卫，在每个路由进入之前校验登录token是否有效，如果有效，表示登录状态有效，此时如果要去的路由是登录，则直接到首页home
if (hasToken) {
  if (to.path === '/login') {
    next({
      path: '/home'
    })
  } else {
    //登录了 则需要校验当前用户有没有对应的路由信息，有的话则直接跳转
    if(personRoutes){
      next()
    }else{
      //登录了但是没有对应路由信息，则获取对应路由信息
      const routes = await getRouterInfo();
      const accessRoutes = await generateRoutes();
      //添加路由信息
      router.addRoutes(accessRoutes);
      next({
        ...to,
        replace: true
      })
    }
  }

}else{

  //如果没有登录，则检查要去的路由是不是在白名单内，也就是无需校验的，在白名单内则直接访问，否则去到登录页
  if (whiteList.indexOf(to.path) !== -1) {
    next()
  } else {
    next(`/login?redirect=${to.path}`)
  }

}

```

## 超过三级嵌套路由，keep-alive 无法缓存

1、把二级的路由 name 和三级路由 name 一块 放到 include 内。
举个例子本来是缓存[menu,menu3],要把二级路由也加上，也就是[menu,menu2,menu3]

2、偷懒一点的方法，去在路由守卫里面，每次跳转的时候根据一些规则把 to.match 里面二级路由去除掉

```
function filterRoutes(routesArr) {
  if (routesArr && routesArr.length) {
    for (let i = 0; i < routesArr.length; i++) {
      const element = routesArr[i]
      if (element.name && element.name.indexOf('blank') != -1) {
        routesArr.splice(i, 1)
        filterRoutes(routesArr)
        break;
      }
    }
  }
}
filterRoutes(to.matched)
next()
```

3、这个方法比较靠谱，大致思路就是把多级嵌套路由拍平成二级嵌套路由，参考[无限级路由缓存方案](https://www.jianshu.com/p/739ae2a73a6d)

```
[
  {
    path: '/menu1',
    component: Layout,
    hidden: true,
    children: [{
      path: 'menu1-1',
      component: () => import('@/views/menu1-1'),
      name: 'menu1-1',
      children: [
        {
          path: 'menu1-1-1',
          component: () => import('@/views/menu1-1-1'),
          name: 'menu1-1-1',
          meta: {
            title: '项目详情1',
            icon: '',
            affix: false,
            tooltip: true
          }
        },
        {
          path: 'menu1-1-2',
          component: () => import('@/views/menu1-1-2'),
          name: 'menu1-1-2',
          meta: {
            title: '项目详情2',
            icon: '',
            affix: false,
            tooltip: true
          }
        },
      ]
    }]
  },
]

三级嵌套路由拍平成二级，变成这样
[
  {
    path: '/menu1',
    component: Layout,
    hidden: true,
    children: [
      {
        path: 'menu1-1/menu1-1-1',
        component: () => import('@/views/1-1-1'),
        name: 'menu1-1-1',
      },
      {
        path: 'menu1-1/menu1-1-2',
        component: () => import('@/views/1-1-2'),
        name: 'menu1-1-2',
      },
    ]
  },
]

//拍平代码如下

/**
 * 生成扁平化机构路由(仅两级结构)
 * @param {允许访问的路由Tree} accessRoutes
 * 路由基本机构:
 * {
 *   name: String,
 *   path: String,
 *   component: Component,
 *   redirect: String,
 *   children: [
 *   ]
 * }
 */
function generateFlatRoutes(accessRoutes) {
  const flatRoutes = []

  for (const item of accessRoutes) {
    let childrenFflatRoutes = []
    if (item.children && item.children.length > 0) {
      childrenFflatRoutes = castToFlatRoute(item.children, '')
    }

    // 一级路由是布局路由,需要处理的只是其子路由数据
    flatRoutes.push({
      name: item.name,
      path: item.path,
      component: item.component,
      redirect: item.redirect,
      children: childrenFflatRoutes
    })
  }

  return flatRoutes
}
/**
 * 将子路由转换为扁平化路由数组（仅一级）
 * @param {待转换的子路由数组} routes
 * @param {父级路由路径} parentPath
 */
function castToFlatRoute(routes, parentPath, flatRoutes = []) {
  for (const item of routes) {
    if (item.children && item.children.length > 0) {
      if (item.redirect && item.redirect !== 'noRedirect') {
        flatRoutes.push({
          name: item.name,
          path: (parentPath + '/' + item.path).substring(1),
          redirect: item.redirect,
          meta: item.meta
        })
      }
      castToFlatRoute(item.children, parentPath + '/' + item.path, flatRoutes)
    } else {
      flatRoutes.push({
        name: item.name,
        path: (parentPath + '/' + item.path).substring(1),
        component: item.component,
        meta: item.meta
      })
    }
  }

  return flatRoutes
}

```

## nvm 安装 node 指定版本报错

Node.js vnode.0.0 is only available in 32-bit.

这时以管理员身份运行 cmd，然后重新 nvm install 指定版本即可

## hexo 博客相关问题和坑

1、hexo deploy 失败

```
  //报错信息如下
  TypeError [ERR_INVALID_ARG_TYPE]: The "mode" argument must be integer.`

  出现该问题的原因是node版本过高，切换版本以后重新安装hexo,再次生成和推送即可

```

2、克隆 hexo 博客项目以后，npm isntall 报错，大致意思就是某个插件下载不下来，就是这个**hexo-asset-image**，这个插件是展示图片用的，在 npm install 以前先 npm install hexo-asset-image --save,否则无法安装成功。

```
git clone git@github.com:ncumovi/blog-by-hexo.git
npm install hexo -g --save
npm install hexo-asset-image --save
npm install
hexo server
```

3、常用 hexo 命令

    hexo new"postName" #新建文章

    hexo new page"pageName" #新建页面

    hexo generate #生成静态页面至public目录

    hexo server #开启预览访问端口 本地服务

    hexo deploy #将.deploy目录部署到GitHub

    hexo help # 查看帮助

    hexo version #查看Hexo的版本

3、hexo 本地图片无法显示

```
//是因为路径写的是相对路径，要改成绝对路径，之前是这样的
![校验数据类型](/img/front-engineer/typeOf.png)
要改成绝对路径
![校验数据类型](img/front-engineer/typeOf.png)
```

## vue 隔代传递数据

当祖先组件使用 provide 提供数据源，子孙组件通 inject 获取数据，但是获取到的数据不是响应式的，也就是说当祖先组件数据源变更的时候，子孙组件获取到的数据不会变化，还是之前的值，为了实现动态响应，可参考 react 传递函数的思路，向子孙组件提供方法而不是直接提供数据，子组件通过 coputed 属性获取数据，还可以用 watch 属性观测,授之于鱼不如授之于渔，具体代码实现如下：

```
//父组件
provide() {
  return {
    getNodeData: () => this.nodeData,
  };
},

//子孙组件
inject:['getNodeData'],
computed:{
  nodeData(){
    return this.getNodeData();
  }
},
watch:{
  nodeData(v){
    //操作
    ...
  }
}
```
