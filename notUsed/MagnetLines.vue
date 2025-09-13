<script setup lang="ts">
import { onMounted, onUnmounted, computed, useTemplateRef } from 'vue';

interface MagnetLinesProps {
  rows?: number;
  columns?: number;
  containerSize?: string;
  lineColor?: string;
  lineWidth?: string;
  lineHeight?: string;
  baseAngle?: number;
  className?: string;
  style?: Record<string, string | number>;
  fullScreen?: boolean;
  fixedMode?: boolean;
  cellSize?: number; // 每个单元格的大小（像素）
  responsive?: boolean; // 是否根据屏幕尺寸调整网格数量
}

const props = withDefaults(defineProps<MagnetLinesProps>(), {
  rows: 9,
  columns: 9,
  containerSize: '80vmin',
  lineColor: '#cccccc',
  lineWidth: '1vmin',
  lineHeight: '6vmin',
  baseAngle: -10,
  className: '',
  style: () => ({}),
  fullScreen: false,
  fixedMode: false,
  cellSize: 80,
  responsive: false
});

const containerRef = useTemplateRef<HTMLDivElement>('containerRef');

const responsiveRows = computed(() => {
  if (!props.responsive) return props.rows;
  
  const screenHeight = window.innerHeight;
  return Math.ceil(screenHeight / props.cellSize);
});

const responsiveColumns = computed(() => {
  if (!props.responsive) return props.columns;
  
  const screenWidth = window.innerWidth;
  return Math.ceil(screenWidth / props.cellSize);
});

const total = computed(() => responsiveRows.value * responsiveColumns.value);

const containerStyle = computed(() => {
  if (props.fixedMode) {
    // 固定模式下使用像素值而不是相对单位
    return {
      gridTemplateColumns: `repeat(${responsiveColumns.value}, 1fr)`,
      gridTemplateRows: `repeat(${responsiveRows.value}, 1fr)`,
      width: `${props.cellSize * responsiveColumns.value}px`,
      height: `${props.cellSize * responsiveRows.value}px`,
      ...props.style
    };
  } else if (props.fullScreen) {
    return {
      gridTemplateColumns: `repeat(${props.columns}, 1fr)`,
      gridTemplateRows: `repeat(${props.rows}, 1fr)`,
      width: '100%',
      height: '100%',
      ...props.style
    };
  } else {
    return {
      gridTemplateColumns: `repeat(${props.columns}, 1fr)`,
      gridTemplateRows: `repeat(${props.rows}, 1fr)`,
      width: props.containerSize,
      height: props.containerSize,
      ...props.style
    };
  }
});

const itemStyle = computed(() => {
  if (props.fixedMode) {
    // 固定模式下使用指定的lineWidth和lineHeight值
    return {
      backgroundColor: props.lineColor,
      width: props.lineWidth,
      height: props.lineHeight,
      '--rotate': `${props.baseAngle}deg`,
      transform: 'rotate(var(--rotate))',
      willChange: 'transform'
    };
  } else {
    return {
      backgroundColor: props.lineColor,
      width: props.lineWidth,
      height: props.lineHeight,
      '--rotate': `${props.baseAngle}deg`,
      transform: 'rotate(var(--rotate))',
      willChange: 'transform'
    };
  }
});

const onPointerMove = (pointer: { x: number; y: number }) => {
  const container = containerRef.value;
  if (!container) return;

  const items = container.querySelectorAll<HTMLSpanElement>('span');

  items.forEach(item => {
    const rect = item.getBoundingClientRect();
    const centerX = rect.x + rect.width / 2;
    const centerY = rect.y + rect.height / 2;

    const b = pointer.x - centerX;
    const a = pointer.y - centerY;
    const c = Math.sqrt(a * a + b * b) || 1;
    const r = ((Math.acos(b / c) * 180) / Math.PI) * (pointer.y > centerY ? 1 : -1);

    item.style.setProperty('--rotate', `${r}deg`);
  });
};

const handlePointerMove = (e: PointerEvent) => {
  onPointerMove({ x: e.x, y: e.y });
};

const handleResize = () => {
  // 窗口大小改变时重新计算位置
  const container = containerRef.value;
  if (!container) return;

  const items = container.querySelectorAll<HTMLSpanElement>('span');
  if (items.length) {
    const middleIndex = Math.floor(items.length / 2);
    const rect = items[middleIndex].getBoundingClientRect();
    onPointerMove({ x: rect.x, y: rect.y });
  }
};

onMounted(() => {
  const container = containerRef.value;
  if (!container) return;

  window.addEventListener('pointermove', handlePointerMove);
  window.addEventListener('resize', handleResize);

  const items = container.querySelectorAll<HTMLSpanElement>('span');
  if (items.length) {
    const middleIndex = Math.floor(items.length / 2);
    const rect = items[middleIndex].getBoundingClientRect();
    onPointerMove({ x: rect.x, y: rect.y });
  }
});

onUnmounted(() => {
  window.removeEventListener('pointermove', handlePointerMove);
  window.removeEventListener('resize', handleResize);
});
</script>

<template>
  <div
    ref="containerRef"
    :class="`grid place-items-center ${props.className}`"
    :style="containerStyle"
  >
    <span
      v-for="i in total"
      :key="i"
      class="block origin-center"
      :style="itemStyle"
    />
  </div>
</template>