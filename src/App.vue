<template>
  <div class="app-container w-full min-h-screen relative">
    <!-- 全屏背景 -->
    <div class="fixed inset-0 -z-10 overflow-hidden bg-black">
      <DotGrid
        :dot-size="4"
        :gap="25"
        base-color="#1a1a1a"
        class-name="w-full h-full"
      />
    </div>
    
    <!-- 主内容区域 - 横向布局 -->
    <div class="relative z-10 w-full min-h-screen flex justify-center items-center p-4">
      <div class="w-[90%] max-w-[1000px] flex justify-center items-center">
        <Stepper
          :initial-step="1"
          :on-step-change="handleStepChange"
          :on-final-step-completed="handleFinalStepCompleted"
          :next-button-props="{ class: nextButtonClass }"
          :lock-on-complete="false"
          :on-next-step="handleNextClick"
          back-button-text="上一步"
          next-button-text="下一步"
          step-circle-container-class-name="w-full max-w-md p-8 rounded-[2rem] shadow-[0_20px_25px_-5px_rgba(0,0,0,0.1),0_10px_10px_-5px_rgba(0,0,0,0.04)] border border-gray-800 bg-black text-white"
          step-container-class-name="flex items-center justify-center w-full mb-8"
          content-class-name="w-full"
          footer-class-name="w-full"
        >
          <div class="p-6 text-center w-full">
            <h2 class="text-2xl font-bold mb-4 text-white">输入卡密</h2>
            <p class="text-gray-200 mb-4">购买后会自动返回一个12位的卡密</p>
            <input
              v-model="key"
              class="mt-4 px-4 py-3 border border-gray-300 rounded-lg w-full max-w-[300px] bg-black/10 text-white focus:outline-none focus:border-[#1246A4] focus:ring-2 focus:ring-[#1246A4]/30"
              placeholder="A1B2C3D4E5F6"
            />
            <p v-if="keyError" class="text-red-500 text-sm mt-2">{{ keyError }}</p>
          </div>
          <div class="p-6 text-center w-full">
            <h2 class="text-2xl font-bold mb-4 text-white">输入设备ID</h2>
            <p class="text-gray-200 mb-4">手环上的二维码扫描出来的数据</p>
            <input
              v-model="deviceId"
              class="mt-4 px-4 py-3 border border-gray-300 rounded-lg w-full max-w-[300px] bg-black/10 text-white focus:outline-none focus:border-[#1246A4] focus:ring-2 focus:ring-[#1246A4]/30"
              placeholder="32位的16进制数据"
            />
            <p v-if="deviceIdError" class="text-red-500 text-sm mt-2">{{ deviceIdError }}</p>
          </div>

          <div class="p-6 text-center w-full">
            <h2 class="text-2xl font-bold mb-4 text-white">你的激活数据</h2>
            <p class="text-gray-200 mb-4">复制数据后前往AstroBox使用插件激活</p>
            <input
              v-model="activationData"
              class="mt-4 px-4 py-3 border border-gray-300 rounded-lg w-full max-w-[300px] bg-black/30 text-white cursor-not-allowed"
              readonly
            />
          </div>

          <div class="p-6 text-center w-full">
            <h2 class="text-2xl font-bold mb-4 text-white">老乡！来钓鱼</h2>
            <p class="text-gray-200 mb-4">你可以加Q群，群号在自动回复里</p>
          </div>
        </Stepper>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import DotGrid from './components/DotGrid.vue';
import Stepper from './components/Stepper.vue';

const key = ref('');
const deviceId = ref('');
const activationData = ref('');
const keyError = ref('');
const deviceIdError = ref('');

// 从URL中提取设备ID参数
const getDeviceIdFromUrl = () => {
  const urlParams = new URLSearchParams(window.location.search);
  return urlParams.get('deviceId');
};

// 验证卡密格式 (12位字母数字组合)
const validateKey = () => {
  const keyRegex = /^[A-Za-z0-9]{12}$/;
  if (!key.value) {
    keyError.value = '请输入卡密';
    return false;
  } else if (!keyRegex.test(key.value)) {
    keyError.value = '卡密格式不正确，应为12位字母或数字';
    return false;
  } else {
    keyError.value = '';
    return true;
  }
};

// 验证设备ID格式 (32位十六进制)
const validateDeviceId = () => {
  const deviceIdRegex = /^[A-Fa-f0-9]{32}$/;
  if (!deviceId.value) {
    deviceIdError.value = '请输入设备ID';
    return false;
  } else if (!deviceIdRegex.test(deviceId.value)) {
    deviceIdError.value = '设备ID格式不正确，应为32位十六进制数据';
    return false;
  } else {
    deviceIdError.value = '';
    return true;
  }
};

// 计算下一步按钮的样式类
const nextButtonClass = computed(() => {
  let baseClass = 'border-none transition-all duration-[350ms] flex items-center justify-center rounded-full font-medium tracking-tight px-3.5 py-1.5 ';
  
  // 根据当前步骤和验证状态决定按钮样式
  if (currentStep.value === 1) {
    // 第一步根据卡密验证状态
    if (!key.value) {
      baseClass += 'bg-gray-400 text-gray-200 cursor-not-allowed';
    } else {
      baseClass += validateKey() 
        ? 'bg-[#1246A4] text-white hover:bg-[#113671] cursor-pointer' 
        : 'bg-gray-400 text-gray-200 cursor-not-allowed';
    }
  } else if (currentStep.value === 2) {
    // 第二步根据设备ID验证状态
    if (!deviceId.value) {
      baseClass += 'bg-gray-400 text-gray-200 cursor-not-allowed';
    } else {
      baseClass += validateDeviceId() 
        ? 'bg-[#1246A4] text-white hover:bg-[#113671] cursor-pointer' 
        : 'bg-gray-400 text-gray-200 cursor-not-allowed';
    }
  } else {
    // 其他步骤使用默认绿色
    baseClass += 'bg-[#1246A4] text-white hover:bg-[#113671] cursor-pointer';
  }
  
  return baseClass;
});

const currentStep = ref(1);

const handleStepChange = (step: number) => {
  currentStep.value = step;
  console.log('Step changed to:', step);
  
  // 清除错误信息
  if (step !== 1) keyError.value = '';
  if (step !== 2) deviceIdError.value = '';
};

// 修改处理下一步点击的逻辑
const handleNextClick = (step: number) => {
  // 根据当前步骤进行验证
  if (step === 1) {
    // 验证卡密
    if (!key.value) {
      keyError.value = '请输入卡密';
      return false;
    }
    
    return validateKey();
  } else if (step === 2) {
    // 验证设备ID
    if (!deviceId.value) {
      deviceIdError.value = '请输入设备ID';
      return false;
    }
    
    return validateDeviceId();
  }
  // 其他步骤直接通过
  return true;
};

const handleFinalStepCompleted = () => {
  console.log('Stepper completed!');
};

// 在组件挂载时检查URL参数
import { onMounted } from 'vue';
onMounted(() => {
  const deviceIdFromUrl = getDeviceIdFromUrl();
  if (deviceIdFromUrl) {
    deviceId.value = deviceIdFromUrl;
  }
});
</script>

<style>
/* 全局重置 */
html, body, #app {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
}

.app-container {
  width: 100%;
  min-height: 100vh;
  position: relative;
}

/* 确保所有文本默认为白色 */
div, span, button {
  color: white;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .stepper-wrapper {
    width: 95%;
  }
  
  .stepper-panel {
    min-width: unset;
    max-width: 100%;
    padding: 1.5rem;
  }
  
  .stepper-indicator {
    gap: 0.5rem;
  }
  
  .step-content {
    padding: 1rem;
  }
}
</style>