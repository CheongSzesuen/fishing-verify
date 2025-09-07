<template>
  <div class="app-container">
    <!-- 全屏背景 -->
    <div class="fixed inset-0 -z-10 overflow-hidden bg-black">
      <MagnetLines 
        full-screen
        fixed-mode
        responsive
        :rows="17"
        :columns="25"
        line-color="#cccccc"
        :base-angle="20"
        :cell-size="70"
        line-width="0.25vmin"
        line-height="5.5vmin"
      />
    </div>
    
    <!-- 主内容区域 - 横向布局 -->
    <div class="relative z-10 w-full min-h-screen flex justify-center items-center p-4">
      <div class="stepper-wrapper">
        <Stepper
          :initial-step="1"
          :on-step-change="handleStepChange"
          :on-final-step-completed="handleFinalStepCompleted"
          back-button-text="Previous"
          next-button-text="Next"
        >
          <div class="step-content">
            <h2>Welcome to the Vue Bits stepper!</h2>
            <p>Check out the next step!</p>
          </div>

          <div class="step-content">
            <h2>Step 2</h2>
            <img
              class="step-image"
              src="https://example.com/image.jpg"
              alt="Example"
            />
            <p>Custom step content!</p>
          </div>

          <div class="step-content">
            <h2>How about an input?</h2>
            <input
              v-model="name"
              class="step-input"
              placeholder="Your name?"
            />
          </div>

          <div class="step-content">
            <h2>Final Step</h2>
            <p>You made it!</p>
          </div>
        </Stepper>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import MagnetLines from './components/MagnetLines.vue';
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