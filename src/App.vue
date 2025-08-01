<script setup>
//局部引入依赖
import * as PIXI from 'pixi.js';
import {onMounted, onBeforeUnmount, ref, shallowRef, watchEffect} from 'vue'
//所有引入
import { Live2DModel } from 'pixi-live2d-display';
//只需要 Cubism 2.1
//import { Live2DModel } from 'pixi-live2d-display/cubism2';
//只需要 Cubism 4
//import { Live2DModel } from 'pixi-live2d-display/cubism4';
//将 PIXI 暴露到 window 对象，以便本插件能够
//引用 window.PIXI.Ticker 来自动更新 Live2D 模型
window.PIXI = PIXI;
//为了存储pixi实例
const app = ref()
//为了存储live2d实例
const model = ref()
//便签绑定
const liveCanvas = shallowRef();
//模型列表
const model_list = ref([
  {model_name: "none", model_path: "none"},
  {model_name: "tiancheng_cv_2", model_path: "/live2d/tiancheng_cv_2/tiancheng_cv_2.model3.json" , scale_set: "0.05" },
  {model_name: "wuzang_3", model_path: "/live2d/wuzang_3/wuzang_3.model3.json", scale_set: "0.1" },
])
// 动作列表数据
const model_motion_list = ref([
  {
    model_name: "none",
    model_list:[
      {motion_name: 'none'},
    ]
  },
  {
    model_name: "wuzang",
    motion_list: [
      {motion_name: 'none'},
      {motion_name: 'complete'},
      {motion_name: 'effect'},
      {motion_name: 'home'},
      {motion_name: 'idle'},
      {motion_name: 'idle1'},
      {motion_name: 'idle2'},
      {motion_name: 'idle3'},
      {motion_name: 'idle4'},
      {motion_name: 'idle5'},
      {motion_name: 'idle6'},
      {motion_name: 'idle7'},
      {motion_name: 'idle8'},
      {motion_name: 'login'},
      {motion_name: 'mail'},
      {motion_name: 'main_1'},
      {motion_name: 'main_2'},
      {motion_name: 'main_3'},
      {motion_name: 'main_4'},
      {motion_name: 'main_5'},
      {motion_name: 'mission'},
      {motion_name: 'mission_complete'},
      {motion_name: 'touch_body'},
      {motion_name: 'touch_drag1'},
      {motion_name: 'touch_drag2'},
      {motion_name: 'touch_drag3'},
      {motion_name: 'touch_drag4'},
      {motion_name: 'touch_drag5'},
      {motion_name: 'touch_drag6'},
      {motion_name: 'touch_drag7'},
      {motion_name: 'touch_drag8'},
      {motion_name: 'touch_drag9'},
      {motion_name: 'touch_drag10'},
      {motion_name: 'touch_drag11'},
      {motion_name: 'touch_drag12'},
      {motion_name: 'touch_drag13'},
      {motion_name: 'touch_drag14'},
      {motion_name: 'touch_head'},
      {motion_name: 'touch_idle1'},
      {motion_name: 'touch_special'},
      {motion_name: 'wedding'}
    ]
  },
  {
    model_name: "tiancheng",
    motion_list: [
      {motion_name: 'none'},
      {motion_name: 'complete'},
      {motion_name: 'effect'},
      {motion_name: 'home'},
      {motion_name: 'idle'},
      {motion_name: 'idle1'},
      {motion_name: 'idle2'},
      {motion_name: 'idle3'},
      {motion_name: 'idle4'},
      {motion_name: 'idle5'},
      {motion_name: 'idle6'},
      {motion_name: 'idle7'},
      {motion_name: 'login'},
      {motion_name: 'mail'},
      {motion_name: 'main_1'},
      {motion_name: 'main_2'},
      {motion_name: 'main_3'},
      {motion_name: 'main_4'},
      {motion_name: 'mission'},
      {motion_name: 'mission_complete'},
      {motion_name: 'touch_body'},
      {motion_name: 'touch_drag1'},
      {motion_name: 'touch_drag3'},
      {motion_name: 'touch_drag4'},
      {motion_name: 'touch_drag5'},
      {motion_name: 'touch_drag6'},
      {motion_name: 'touch_drag7'},
      {motion_name: 'touch_drag8'},
      {motion_name: 'touch_drag9'},
      {motion_name: 'touch_drag10'},
      {motion_name: 'touch_drag11'},
      {motion_name: 'touch_drag12'},
      {motion_name: 'touch_drag13'},
      {motion_name: 'touch_drag14'},
      {motion_name: 'touch_drag15'},
      {motion_name: 'touch_head'},
      {motion_name: 'touch_special'},
      {motion_name: 'wedding'}
    ]
  }
]);
//模型数字
const model_num = ref(0)
//模型是否运动
const is_moving = ref()
//是否切换模型
const is_change_model = ref(false)
//模型动作相关
const model_motion = ref({
  // 当前选择的动作
  selectedMotion: 'none',
  // 当前动作编号
  motionNum: 0,
})
//模型创建时间
const model_elapsedTime = ref('none')
// 加载模型的函数
const loadModel = async (index) => {
  // 如果已经有模型存在，先销毁
  if (model.value) {
    app.value.stage.removeChild(model.value);
    model.value.destroy();
    model.value = null;
  }
  // 加载新模型
  if (model_list.value[index].model_path !== "none") {
    // 这里是放live2d资源的地方，直接相对路径引用即可
    model.value = await Live2DModel.from(model_list.value[index].model_path); //导入后缀为.model3.json的json文件路径
    app.value.stage.addChild(model.value);// 直接引入模型
    model.value.scale.set(model_list.value[index].scale_set); // 调整缩放比例,0.1-0.2整体比较合适,要每个模型具体调整
    // model.value.position.set(window.innerWidth / 5, window.innerHeight / 60); // 调整渲染位置,要每个模型具体调整
    model.value.autoInteract = true; // 启用自动交互
  }
  //模型构建时间
  if (!model.value) return
    model_elapsedTime.value = model.value.elapsedTime
    console.log('模型数据',model.value)
  // 添加点击事件监听
  model.value.on('pointerdown', (event) => {
    console.log('模型被点击', event.data.global);
    // 触发点击动作（例如触摸身体）
    if (model.value && is_moving.value === false ) {
      console.log('当前正在执行动作')
      return
    }
    model.value.motion('idle');
  });
};
// 执行动作函数
const execute_action = async (selectedMotion, motionNum) => {
  if (!model.value) return;
  model.value.motion(selectedMotion,  motionNum);
};
//页面dom构建完成就执行
onMounted(async () => {
  app.value = new PIXI.Application({
    view: liveCanvas.value, //ref组件绑定，liveCanvas为下文自定义的
    autoStart: true, //是否开启自动播放
    //resizeTo: window, //是否覆盖样式
    interaction: true, // 确保启用交互系统
    backgroundAlpha: 0, //背景
    resolution: window.devicePixelRatio || 1, // 使用设备像素比提高清晰度
    antialias: true, // 开启抗锯齿
  })
  // 初始加载模型
  await loadModel(model_num.value);
  //监听动画
  watchEffect( ()=> {
    if (!model.value || !model.value.internalModel) return;
    is_moving.value = model.value.internalModel?.motionManager.isFinished()
    console.log('执行监听动画',is_moving.value,is_change_model.value  )
  })
  //监视模型切换
  watchEffect(async ()=> {
    if (!model.value) return;
    console.log('执行监听模型')
  })
  //监视时间
  watchEffect(async ()=> {
    if (!model.value) return
    model_elapsedTime.value = Math.floor(parseFloat(model.value.elapsedTime))
    console.log('时间',model_elapsedTime.value)
  })
})
onBeforeUnmount(() => {
  model?.value.destroy()
  app?.value.destroy()
})
//更换模型
const change_model = async () => {
  await loadModel(model_num.value);
  is_change_model.value = true;
}
// 更换动作
const change_model_move = () => {
  execute_action(model_motion.value.selectedMotion, model_motion.value.motionNum);
};
//停止动作
const motion_stop = () => {
  // 停止所有当前动作
  model.value.internalModel.motionManager.stopAllMotions();
}
//测试函数
const test_func = () => {
  if (!model.value) return;
  console.log('当前音频', model.value.internalModel.motionManager.currentAudio)
}
</script>

<template>
  <div class="background">
    <div class="top">
      <div class="model_size">
        <!--切换模型-->
        <select v-model="model_num" @change="change_model">
          <option v-for="(model , index) in model_list" :value="index" :key="index">
            {{model.model_name}}
          </option>
        </select>
        <!--更换动作-->
        <select :disabled="is_moving === true && is_change_model === false" v-model="model_motion.selectedMotion" @change="change_model_move">
          <option v-for="motion in model_motion_list[model_num].motion_list" :value="motion.motion_name" :key="motion.motion_name">
            {{motion.motion_name}}
          </option>
        </select>
        <button @click="motion_stop">停止动作</button>
        <button @click="test_func">test</button>
        <div style="font-size: 10px">非必要请不要停止动作</div>
      </div>
      <div class="top_name">这是live2d-vue一键部署项目</div>
      <div class="model_data">
        <div>当前模型:&nbsp;{{model_list[model_num].model_name}}</div>
        <div>当前动作:&nbsp;{{model_motion.selectedMotion}}</div>
        <div>创建时间&nbsp;{{ model_elapsedTime }}毫秒</div>
      </div>
    </div>
    <div class="live2d-box">
      <canvas class="live2d-box-canvas" ref="liveCanvas"></canvas>
    </div>
  </div>
</template>

<style scoped>
.background {
  box-sizing: border-box;
  width: 100vw;
  height: 100vh;
  background-color: aquamarine;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  overflow: hidden;
}
.top {
  box-sizing: border-box;
  height: 30px;
  display: flex;
  flex-direction: row;
  width: 100%;
  align-items: center;
  justify-content: center;
  gap: 10px;
}
.model_size {
  width: 450px;
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 10px;
}
.top_name {
  flex-grow: 1;
  width: auto;
  display: flex;
  align-items: center;
  justify-content: center;
}
.model_data {
  width: 450px;
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 10px;
}
.live2d-box {
  width: 100%;
  height: calc(100% - 30px);
  display: flex;
  justify-content: center;
  align-items: center;
}
.live2d-box-canvas {
  height: 100%;
}
</style>
