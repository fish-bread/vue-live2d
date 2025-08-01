# vue-live2d

本项目是基于[guansss](https://github.com/guansss)大佬(感谢大佬)的[pixi-live2d-display](https://github.com/guansss/pixi-live2d-display)项目开发,适用于vue3的live2d-web项目

制作本项目的初衷是获得一个可以自主修改样式的live2d组件(尽管市面上已经有web_live2d项目了)。 根据此项目,你大概可以做出属于你自己的vue_live2d组件,你可以自己修改样式或者优化函数

本项目使用的api文档为 https://guansss.github.io/pixi-live2d-display/

## 目前的功能

- 可以实现渲染live2d;可以根据列表切换加载的模型;可以根据模型的不同,匹配不同的动作列表;可以暂停动作
- 本项目初始提供两个live2d文件,但只有一个拥有我手工添加过语音的live2d文件
- 本人获得的碧蓝航线live2d包,在这里就放上百度链接供各位使用。(文件较大不必一次下完,寻找你需要的模型下载)
- 碧蓝live2d模型 链接: https://pan.baidu.com/s/1-QgvflZa9fl6ffVjyZWP6w?pwd=1903

### 版权声明

- 本项目引用的 `pixi-live2d-display` 已遵循其 [MIT 开源协议](https://github.com/guansss/pixi-live2d-display/blob/main/LICENSE)，请确保使用时保留原始版权声明。
- **《碧蓝航线》Live2D模型**版权归属于游戏开发商（上海蛮啾网络），本仓库提供的资源**仅限学习交流**，禁止用于任何商业用途。若版权方提出异议，将立即删除相关内容
- 使用者需自行承担因解包、分发或使用游戏资源可能引发的法律风险。

## 缺点

1. 导入的live2d文件如果没有语音,那就必须去寻找对应动作的对应语音并补全live2d文件,非常麻烦
2. 如果需要添加新模型,必须手动添加动作列表到model_motion_list中,但electron或node环境下可以使用fs来自动读取(包括模型名)
3. 没有实现模型表情切换
4. 没有实现操作live2d模型的缩放和移动等功能
5. 界面丑陋,还有一些小bug

## 未来优化

优化界面,优化逻辑,实现组件化便于直接使用,寻找显示表情的可能性

## 项目初始化

```sh
npm install
```

### 开发模式（热更新）

```sh
npm run dev
```

### 生产环境构建（压缩优化）

```sh
npm run build
```

### eslint代码规范检查

```sh
npm run lint
```
