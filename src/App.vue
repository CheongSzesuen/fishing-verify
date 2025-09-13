<template>
  <div class="app-container">
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
      <div class="stepper-wrapper">
        <Stepper
          :initial-step="1"
          :on-step-change="handleStepChange"
          :on-final-step-completed="handleFinalStepCompleted"
          back-button-text="上一步"
          next-button-text="下一步"
        >
          <div class="step-content">
            <h2>输入卡密</h2>
            <p>购买后会自动返回一个12位的卡密</p>
            <input
              v-model="name"
              class="step-input"
              placeholder="A1B2C3D4E5F6"
            />
          </div>
<div class="step-content">
            <h2>输入设备ID</h2>
            <p>手环上的二维码扫描出来的数据</p>
            <input
              v-model="name"
              class="step-input"
              placeholder="32位的16进制数据"
            />
          </div>


          <div class="step-content">
            <h2>你的激活数据</h2>
            <p>复制数据后前往AstroBox使用插件激活</p>
            <input
              v-model="name"
              class="step-input"
            />
          </div>

          <div class="step-content">
            <h2>老乡！欢迎来钓鱼</h2>
            <p>你可以加Q群，群号在自动回复里</p>
          </div>
        </Stepper>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import DotGrid from './components/DotGrid.vue';
import Stepper from './components/Stepper.vue';

const name = ref('');

const handleStepChange = (step: number) => {
  console.log('Step changed to:', step);
};

const handleFinalStepCompleted = () => {
  console.log('Stepper completed!');
};
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

/* Stepper包装器 - 横向布局 */
.stepper-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 90%;
  max-width: 1000px; /* 增加最大宽度以适应横向布局 */
}

/* Stepper面板样式 - 横向长方形 */
.stepper-panel {
  width: auto !important;
  min-width: 600px; /* 增加最小宽度 */
  max-width: 900px; /* 增加最大宽度 */
  padding: 2rem 3rem; /* 增加水平内边距 */
  border-radius: 1.5rem;
  background: rgba(6, 78, 59, 0.7);
  backdrop-filter: blur(12px);
  border: 1px solid rgba(34, 197, 94, 0.3);
  box-shadow: 
    0 20px 25px -5px rgba(0, 0, 0, 0.5),
    0 10px 10px -5px rgba(0, 0, 0, 0.4);
}

/* 步骤指示器横向排列 */
.stepper-indicator {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 1rem;
  margin-bottom: 2rem;
}

.stepper-indicator .step {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 500;
  transition: all 0.3s ease;
}

.stepper-indicator .step.active {
  background: #10b981;
  color: white;
}

.stepper-indicator .step.complete {
  background: #10b981;
  color: white;
}

.stepper-indicator .step.inactive {
  background: #374151;
  color: #9ca3af;
}

/* 步骤连接线横向排列 */
.stepper-connector {
  flex: 1;
  height: 2px;
  background: #4b5563;
  margin: 0 0.5rem;
  position: relative;
}

.stepper-connector .progress {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  background: #10b981;
  transition: width 0.3s ease;
}

/* 步骤内容横向布局 */
.step-content-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  min-height: 200px;
}

.step-content {
  padding: 1.5rem;
  text-align: center;
  width: 100%;
}

.step-content h2 {
  font-size: 1.5rem;
  font-weight: bold;
  margin-bottom: 1rem;
  color: white;
}

.step-content p {
  font-size: 1rem;
  color: #e5e7eb;
  margin-bottom: 1rem;
}

.step-image {
  height: 120px;
  width: 100%;
  max-width: 400px;
  object-fit: cover;
  border-radius: 15px;
  margin: 1em auto;
}

.step-input {
  margin-top: 1rem;
  padding: 0.75rem 1rem;
  border: 1px solid #d1d5db;
  border-radius: 0.5rem;
  width: 100%;
  max-width: 300px;
  background: rgba(255, 255, 255, 0.1);
  color: white;
}

.step-input:focus {
  outline: none;
  border-color: #10b981;
  box-shadow: 0 0 0 2px rgba(16, 185, 129, 0.3);
}

/* 底部按钮区域 */
.stepper-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  margin-top: 2rem;
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