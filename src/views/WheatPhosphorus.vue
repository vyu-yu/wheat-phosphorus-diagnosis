<template>
  <div class="app-container">
    <!-- 大标题区域 -->
    <div class="main-header">
      <div class="header-glow"></div>
      <h1 class="main-title">
        <span class="title-icon">🌾</span>
        小麦磷素营养规则诊断系统
        <span class="title-badge">规则诊断演示</span>
      </h1>
      <p class="main-subtitle">Vue 3 · ECharts · Interactive Data Visualization</p>
    </div>

    <!-- 顶部信息栏 -->
    <header class="header">
      <div class="header-content">
        <div class="logo-section">
          <img :src="imagePath('logo.png')" alt="项目标志" class="logo" />
          <div class="system-info">
            <span class="version-tag">v1.0.0</span>
            <span class="date-tag">{{ currentDate }}</span>
          </div>
        </div>
        <div class="header-stats">
          <div class="stat-item">
            <span class="stat-icon">📊</span>
            <span class="stat-text">参数诊断</span>
          </div>
          <div class="stat-item">
            <span class="stat-icon">🔬</span>
            <span class="stat-text">规则评估</span>
          </div>
          <div class="stat-item">
            <span class="stat-icon">💡</span>
            <span class="stat-text">施肥建议</span>
          </div>
        </div>
      </div>
    </header>

    <!-- 主体内容 -->
    <main class="main-content">
      <!-- 左侧：数据输入区 -->
      <aside class="input-panel">
        <div class="panel-card">
          <h2 class="panel-title">
            <span class="icon">📊</span>
            数据采集
            <span class="title-line"></span>
          </h2>

          <!-- 土壤磷含量 -->
          <div class="input-group">
            <label class="input-label">
              <span class="label-text">土壤有效磷含量</span>
              <span class="label-unit">(mg/kg)</span>
            </label>
            <div class="slider-container">
              <input 
                type="range" 
                min="0" 
                max="50" 
                step="0.5"
                v-model="soilP" 
                class="slider"
                :style="{'--value': soilP / 50 * 100 + '%'}"
              />
              <div class="slider-value">{{ soilP }}</div>
            </div>
            <div class="slider-marks">
              <span>0</span>
              <span>25</span>
              <span>50</span>
            </div>
          </div>

          <!-- 植株磷含量 -->
          <div class="input-group">
            <label class="input-label">
              <span class="label-text">植株全磷含量</span>
              <span class="label-unit">(%)</span>
            </label>
            <div class="slider-container">
              <input 
                type="range" 
                min="0" 
                max="1" 
                step="0.01"
                v-model="plantP" 
                class="slider"
                :style="{'--value': plantP / 1 * 100 + '%'}"
              />
              <div class="slider-value">{{ plantP }}</div>
            </div>
            <div class="slider-marks">
              <span>0</span>
              <span>0.5</span>
              <span>1.0</span>
            </div>
          </div>

          <!-- 生长阶段选择 -->
          <div class="input-group">
            <label class="input-label">
              <span class="label-text">生长阶段</span>
            </label>
            <div class="stage-buttons">
              <button 
                v-for="stage in stages" 
                :key="stage.value"
                :class="['stage-btn', { active: currentStage === stage.value }]"
                @click="currentStage = stage.value"
              >
                {{ stage.label }}
              </button>
            </div>
          </div>

          <!-- 快速诊断按钮 -->
          <button class="diagnose-btn" @click="runDiagnosis">
            <span class="btn-glow"></span>
            <span class="btn-icon">🔬</span>
            开始诊断
            <span class="btn-arrow">→</span>
          </button>
        </div>

        <!-- 小麦图片展示 -->
        <div class="panel-card wheat-display">
          <h3 class="mini-title">
            <span class="icon">🌱</span>
            小麦形态
          </h3>
          <div class="wheat-images">
            <div class="wheat-img-item">
              <div class="img-wrapper">
                <img :src="imagePath('wheat-plant.png')" alt="小麦植株" loading="lazy" />
              </div>
              <span>植株</span>
            </div>
            <div class="wheat-img-item">
              <div class="img-wrapper">
                <img :src="imagePath('wheat-root.png')" alt="小麦根系" loading="lazy" />
              </div>
              <span>根系</span>
            </div>
            <div class="wheat-img-item">
              <div class="img-wrapper">
                <img :src="imagePath('wheat-leaf.png')" alt="小麦叶片" loading="lazy" />
              </div>
              <span>叶片</span>
            </div>
          </div>
        </div>
      </aside>

      <!-- 右侧：诊断结果展示区 -->
      <section class="result-panel">
        <!-- 诊断结果卡片 -->
        <div class="result-card diagnosis-result" :class="diagnosisLevel">
          <div class="result-header">
            <h2 class="result-title">
              <span class="status-icon">{{ diagnosisIcon }}</span>
              诊断结论
            </h2>
            <span class="status-badge" :class="diagnosisLevel">
              {{ diagnosisLevelText }}
            </span>
          </div>
          <p class="diagnosis-text">{{ diagnosisText }}</p>
          <div class="diagnosis-details">
            <div class="detail-item">
              <span class="detail-label">土壤有效磷</span>
              <span class="detail-value">{{ soilP }} <span class="unit">mg/kg</span></span>
            </div>
            <div class="detail-item">
              <span class="detail-label">植株全磷</span>
              <span class="detail-value">{{ plantP }} <span class="unit">%</span></span>
            </div>
            <div class="detail-item">
              <span class="detail-label">生长阶段</span>
              <span class="detail-value">{{ currentStageName }}</span>
            </div>
          </div>
        </div>

        <!-- 图表选项卡 -->
        <div class="chart-tabs">
          <button 
            v-for="tab in chartTabs" 
            :key="tab.value"
            :class="['tab-btn', { active: activeChart === tab.value }]"
            @click="activeChart = tab.value"
          >
            <span class="tab-icon">{{ tab.icon }}</span>
            <span class="tab-text">{{ tab.label }}</span>
          </button>
        </div>

        <!-- 图表展示区 -->
        <div class="result-card chart-card">
          <div v-show="activeChart === 'bar'" class="chart-wrapper">
            <div ref="barChartRef" class="chart-box"></div>
          </div>
          <div v-show="activeChart === 'line'" class="chart-wrapper">
            <div ref="lineChartRef" class="chart-box"></div>
          </div>
          <div v-show="activeChart === 'radar'" class="chart-wrapper">
            <div ref="radarChartRef" class="chart-box"></div>
          </div>
        </div>

        <!-- 建议措施 -->
        <div class="result-card suggestions">
          <h3 class="mini-title">
            <span class="icon">💡</span>
            施肥建议
            <span class="title-line"></span>
          </h3>
          <ul class="suggestion-list">
            <li v-for="(suggestion, index) in suggestions" :key="index">
              <span class="suggestion-number">{{ index + 1 }}</span>
              {{ suggestion }}
            </li>
          </ul>
        </div>
      </section>
    </main>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount, watch } from 'vue'
import * as echarts from 'echarts'

// ========== 数据定义 ==========
const soilP = ref(20)
const plantP = ref(0.25)
const currentStage = ref('jointing')
const activeChart = ref('bar')
const currentDate = ref('')
const imagePath = (filename) => `${import.meta.env.BASE_URL}images/${filename}`

const stages = [
  { label: '苗期', value: 'seedling' },
  { label: '分蘖期', value: 'tillering' },
  { label: '拔节期', value: 'jointing' },
  { label: '抽穗期', value: 'heading' },
  { label: '灌浆期', value: 'filling' }
]

const chartTabs = [
  { label: '柱状对比', value: 'bar', icon: '📊' },
  { label: '趋势分析', value: 'line', icon: '📈' },
  { label: '综合评估', value: 'radar', icon: '🎯' }
]

// ========== 图表引用 ==========
const barChartRef = ref(null)
const lineChartRef = ref(null)
const radarChartRef = ref(null)
let barChart, lineChart, radarChart
let chartInitTimer = null
let chartResizeTimer = null

// ========== 计算属性 ==========
const currentStageName = computed(() => {
  return stages.find(s => s.value === currentStage.value)?.label || '未知'
})

const diagnosisLevel = computed(() => {
  if (soilP.value < 10 || plantP.value < 0.15) return 'severe'
  if (soilP.value < 20 || plantP.value < 0.25) return 'moderate'
  return 'normal'
})

const diagnosisLevelText = computed(() => {
  const levels = {
    severe: '严重缺磷',
    moderate: '磷素偏低',
    normal: '磷素适宜'
  }
  return levels[diagnosisLevel.value]
})

const diagnosisIcon = computed(() => {
  const icons = {
    severe: '⚠️',
    moderate: '⚡',
    normal: '✅'
  }
  return icons[diagnosisLevel.value]
})

const diagnosisText = computed(() => {
  const soil = soilP.value
  const plant = plantP.value
  
  if (soil < 10 || plant < 0.15) {
    return `当前土壤有效磷含量为 ${soil} mg/kg，植株全磷含量为 ${plant}%，均低于临界值。磷素严重不足会导致根系发育不良、分蘖能力减弱、抗逆性下降。建议立即增施磷肥，并配合有机肥改善土壤磷素供应能力。`
  }
  
  if (soil < 20 || plant < 0.25) {
    return `当前土壤有效磷含量为 ${soil} mg/kg，植株全磷含量为 ${plant}%，处于偏低水平。磷素偏低可能影响小麦后期生长，建议适量补充磷肥，防止生育后期磷素亏缺影响产量和品质。`
  }
  
  return `当前土壤有效磷含量为 ${soil} mg/kg，植株全磷含量为 ${plant}%，均处于适宜范围。磷素水平能够满足小麦当前生长需求，继续维持现有施肥方案即可。`
})

const suggestions = computed(() => {
  const level = diagnosisLevel.value
  
  const baseSuggestions = {
    severe: [
      '基肥增施过磷酸钙 40-50 kg/亩或磷酸二铵 20-25 kg/亩',
      '追肥时配合施用速效磷肥，建议叶面喷施磷酸二氢钾溶液',
      '配施有机肥 1000-1500 kg/亩，改善土壤磷素活化能力',
      '注意与氮肥配合施用，促进磷素吸收利用'
    ],
    moderate: [
      '基肥施用过磷酸钙 30-35 kg/亩或磷酸二铵 15-18 kg/亩',
      '在分蘖期和拔节期各追施一次速效磷肥',
      '配合施用适量有机肥，提高土壤磷素供应能力',
      '监测后期磷素状况，必要时进行叶面追肥'
    ],
    normal: [
      '维持现有施肥方案，基肥施用过磷酸钙 25-30 kg/亩',
      '定期监测土壤和植株磷素状况',
      '平衡施用氮磷钾肥，避免养分失衡',
      '注意土壤培肥，保持良好的磷素供应能力'
    ]
  }
  
  return baseSuggestions[level]
})

// ========== 生命周期 ==========
onMounted(() => {
  // 初始化日期
  const now = new Date()
  currentDate.value = `${now.getFullYear()}-${String(now.getMonth() + 1).padStart(2, '0')}-${String(now.getDate()).padStart(2, '0')}`
  
  // 先初始化图表实例，再更新数据，最后resize
  chartInitTimer = window.setTimeout(() => {
    initCharts() // 1. 创建实例
    updateAllCharts() // 2. 设置图表数据
    // 3. 强制resize适配宽度
    if (barChart) barChart.resize()
    if (lineChart) lineChart.resize()
    if (radarChart) radarChart.resize()
    
    window.addEventListener('resize', handleChartResize)
  }, 300) // 缩短延迟，避免渲染过晚
})

onBeforeUnmount(() => {
  window.clearTimeout(chartInitTimer)
  window.clearTimeout(chartResizeTimer)
  window.removeEventListener('resize', handleChartResize)
  if (barChart) barChart.dispose()
  if (lineChart) lineChart.dispose()
  if (radarChart) radarChart.dispose()
})

// 监听标签切换
watch(activeChart, (val) => {
  // 先确保图表实例初始化完成
  if (!barChart || !lineChart || !radarChart) {
    initCharts()
  }

  if (val === 'line') {
    // 先更新图表配置，再resize（顺序反了会导致无数据）
    updateLineChart()
    // 延迟100ms resize，确保配置生效
    setTimeout(() => {
      if (lineChart) {
        lineChart.resize()
      }
    }, 100)
  } else if (val === 'bar') {
    updateBarChart()
    setTimeout(() => {
      if (barChart) barChart.resize()
    }, 100)
  } else if (val === 'radar') {
    updateRadarChart()
    setTimeout(() => {
      if (radarChart) radarChart.resize()
    }, 100)
  }
})

// ========== 方法 ==========
function handleChartResize() {
  window.clearTimeout(chartResizeTimer)
  chartResizeTimer = window.setTimeout(() => {
    if (barChart) barChart.resize()
    if (lineChart) lineChart.resize()
    if (radarChart) radarChart.resize()
  }, 120)
}

function initCharts() {
  // 先销毁旧实例
  if (barChart) barChart.dispose()
  if (lineChart) lineChart.dispose()
  if (radarChart) radarChart.dispose()

  // 回归基础初始化方式（取消强制width，避免实例创建失败）
  if (barChartRef.value) barChart = echarts.init(barChartRef.value)
  if (lineChartRef.value) lineChart = echarts.init(lineChartRef.value)
  if (radarChartRef.value) radarChart = echarts.init(radarChartRef.value)
}

function updateAllCharts() {
  updateBarChart()
  updateLineChart()
  updateRadarChart()
}

function updateBarChart() {
  if (!barChart) return
  
  const option = {
    title: {
      text: '磷素含量对比分析',
      left: 'center',
      top: 15,
      textStyle: { 
        fontSize: 16, 
        fontWeight: 'bold',
        color: '#2c3e50'
      }
    },
    tooltip: {
      trigger: 'axis',
      axisPointer: { type: 'shadow' },
      backgroundColor: 'rgba(255, 255, 255, 0.95)',
      borderColor: '#4CAF50',
      borderWidth: 1,
      textStyle: { color: '#333' }
    },
    legend: {
      data: ['当前值', '临界值', '适宜值'],
      bottom: 15,
      textStyle: { fontSize: 12 }
    },
    grid: {
      left: '3%',
      right: '4%',
      bottom: '15%',
      top: '20%',
      containLabel: true
    },
    xAxis: {
      type: 'category',
      data: ['土壤有效磷 (mg/kg)', '植株全磷 (%)'],
      axisLine: { lineStyle: { color: '#ddd' } },
      axisLabel: { color: '#666', fontSize: 11 }
    },
    yAxis: {
      type: 'value',
      axisLine: { lineStyle: { color: '#ddd' } },
      axisLabel: { color: '#666' },
      splitLine: { lineStyle: { color: '#f0f0f0' } }
    },
    series: [
      {
        name: '当前值',
        type: 'bar',
        data: [soilP.value, plantP.value * 100],
        itemStyle: { 
          color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
            { offset: 0, color: '#4CAF50' },
            { offset: 1, color: '#81C784' }
          ])
        },
        barWidth: '30%'
      },
      {
        name: '临界值',
        type: 'bar',
        data: [10, 15],
        itemStyle: { 
          color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
            { offset: 0, color: '#FF5252' },
            { offset: 1, color: '#FF8A80' }
          ])
        },
        barWidth: '30%'
      },
      {
        name: '适宜值',
        type: 'bar',
        data: [20, 25],
        itemStyle: { 
          color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
            { offset: 0, color: '#66BB6A' },
            { offset: 1, color: '#A5D6A7' }
          ])
        },
        barWidth: '30%'
      }
    ]
  }
  
  barChart.setOption(option)
}

function updateLineChart() {
  if (!lineChart) return
  
  const days = ['第1天', '第7天', '第14天', '第21天', '第28天', '当前']
  const soilData = [18, 19, 19.5, 20, 20, soilP.value]
  const plantData = [0.20, 0.22, 0.23, 0.24, 0.25, plantP.value]
  
  const option = {
    title: {
      text: '磷素含量变化趋势',
      left: 'center',
      top: 15,
      textStyle: { 
        fontSize: 16, 
        fontWeight: 'bold',
        color: '#2c3e50'
      }
    },
    tooltip: {
      trigger: 'axis',
      backgroundColor: 'rgba(255, 255, 255, 0.95)',
      borderColor: '#4CAF50',
      borderWidth: 1,
      textStyle: { color: '#333' }
    },
    legend: {
      data: ['土壤有效磷', '植株全磷'],
      bottom: 15,
      textStyle: { fontSize: 12 }
    },
    // 先恢复基础grid（和柱状图一致，确保显示）
    grid: {
      left: '3%',
      right: '4%',
      bottom: '15%',
      top: '20%',
      containLabel: true
    },
    xAxis: {
      type: 'category',
      data: days,
      boundaryGap: false,
      axisLine: { lineStyle: { color: '#ddd' } },
      axisLabel: { color: '#666', fontSize: 11 }
    },
    yAxis: [
      {
        type: 'value',
        name: '土壤磷 (mg/kg)',
        position: 'left',
        axisLine: { lineStyle: { color: '#4CAF50' } },
        axisLabel: { color: '#666' },
        splitLine: { lineStyle: { color: '#f0f0f0' } }
      },
      {
        type: 'value',
        name: '植株磷 (%)',
        position: 'right',
        axisLine: { lineStyle: { color: '#8BC34A' } },
        axisLabel: { color: '#666' },
        splitLine: { show: false }
      }
    ],
    series: [
      {
        name: '土壤有效磷',
        type: 'line',
        data: soilData,
        smooth: true,
        itemStyle: { color: '#4CAF50' },
        lineStyle: { width: 3 },
        areaStyle: {
          color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
            { offset: 0, color: 'rgba(76, 175, 80, 0.3)' },
            { offset: 1, color: 'rgba(76, 175, 80, 0.05)' }
          ])
        }
      },
      {
        name: '植株全磷',
        type: 'line',
        yAxisIndex: 1,
        data: plantData,
        smooth: true,
        itemStyle: { color: '#8BC34A' },
        lineStyle: { width: 3 },
        areaStyle: {
          color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
            { offset: 0, color: 'rgba(139, 195, 74, 0.3)' },
            { offset: 1, color: 'rgba(139, 195, 74, 0.05)' }
          ])
        }
      }
    ]
  }
  
  // 强制清空旧配置，重新设置（避免配置冲突）
  lineChart.clear()
  lineChart.setOption(option, true)
}

function updateRadarChart() {
  if (!radarChart) return
  
  const indicators = [
    { name: '土壤磷素', max: 50 },
    { name: '植株磷素', max: 1 },
    { name: '根系发育', max: 100 },
    { name: '分蘖能力', max: 100 },
    { name: '抗逆性', max: 100 }
  ]
  
  const rootDev = Math.min(100, soilP.value * 3)
  const tillering = Math.min(100, plantP.value * 300)
  const resistance = Math.min(100, (soilP.value * 2 + plantP.value * 100) / 2)
  
  const option = {
    title: {
      text: '小麦磷素营养综合评估',
      left: 'center',
      top: 15,
      textStyle: { 
        fontSize: 16, 
        fontWeight: 'bold',
        color: '#2c3e50'
      }
    },
    tooltip: {
      backgroundColor: 'rgba(255, 255, 255, 0.95)',
      borderColor: '#4CAF50',
      borderWidth: 1,
      textStyle: { color: '#333' }
    },
    legend: {
      data: ['当前状态'],
      bottom: 15,
      textStyle: { fontSize: 12 }
    },
    radar: {
      indicator: indicators,
      center: ['50%', '55%'],
      radius: '60%',
      splitNumber: 4,
      name: {
        textStyle: { 
          color: '#666',
          fontSize: 12,
          fontWeight: 500
        }
      },
      splitLine: {
        lineStyle: { color: '#e0e0e0' }
      },
      splitArea: {
        areaStyle: {
          color: ['rgba(76, 175, 80, 0.05)', 'rgba(76, 175, 80, 0.1)']
        }
      },
      axisLine: {
        lineStyle: { color: '#d0d0d0' }
      }
    },
    series: [
      {
        name: '营养指标',
        type: 'radar',
        data: [
          {
            value: [soilP.value, plantP.value, rootDev, tillering, resistance],
            name: '当前状态'
          }
        ],
        itemStyle: { 
          color: '#4CAF50',
          borderColor: '#4CAF50',
          borderWidth: 2
        },
        lineStyle: {
          width: 3,
          color: '#4CAF50'
        },
        areaStyle: { 
          color: new echarts.graphic.RadialGradient(0.5, 0.5, 1, [
            { offset: 0, color: 'rgba(76, 175, 80, 0.4)' },
            { offset: 1, color: 'rgba(76, 175, 80, 0.1)' }
          ])
        }
      }
    ]
  }
  
  radarChart.setOption(option)
}

function runDiagnosis() {
  const cards = document.querySelectorAll('.result-card')
  cards.forEach((card, index) => {
    card.style.animation = 'none'
    setTimeout(() => {
      card.style.animation = 'slideIn 0.5s ease-out forwards'
    }, index * 100)
  })
  
  updateAllCharts()
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+SC:wght@300;400;500;600;700&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.app-container {
  min-height: 100vh;
  background: linear-gradient(135deg, #1a5f4d 0%, #2d8659 50%, #3fa06b 100%);
  position: relative;
  overflow-x: hidden;
}

/* ========== 大标题区域 ========== */
.main-header {
  position: relative;
  text-align: center;
  padding: 40px 20px 30px;
  background: linear-gradient(180deg, rgba(0,0,0,0.3) 0%, rgba(0,0,0,0) 100%);
  overflow: hidden;
}

.header-glow {
  position: absolute;
  top: -50%;
  left: 50%;
  transform: translateX(-50%);
  width: 600px;
  height: 600px;
  background: radial-gradient(circle, rgba(76, 175, 80, 0.3) 0%, transparent 70%);
  pointer-events: none;
}

.main-title {
  font-size: 42px;
  font-weight: 700;
  color: #fff;
  text-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 16px;
  flex-wrap: wrap;
  position: relative;
  z-index: 1;
}

.title-icon {
  font-size: 48px;
  animation: float 3s ease-in-out infinite;
}

@keyframes float {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}

.title-badge {
  font-size: 14px;
  font-weight: 600;
  background: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(10px);
  padding: 6px 16px;
  border-radius: 20px;
  border: 1px solid rgba(255, 255, 255, 0.3);
  letter-spacing: 1px;
}

.main-subtitle {
  font-size: 16px;
  font-weight: 400;
  color: rgba(255, 255, 255, 0.9);
  letter-spacing: 2px;
  text-transform: uppercase;
  position: relative;
  z-index: 1;
}

/* ========== 顶部信息栏 ========== */
.header {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  box-shadow: 0 4px 24px rgba(0, 0, 0, 0.1);
  padding: 0 32px;
  position: sticky;
  top: 0;
  z-index: 100;
}

.header-content {
  max-width: 1600px;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 65px;
}

.logo-section {
  display: flex;
  align-items: center;
  gap: 16px;
}

.logo {
  width: 45px;
  height: 45px;
  border-radius: 10px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.logo-placeholder {
  width: 45px;
  height: 45px;
  background: linear-gradient(135deg, #4CAF50, #66BB6A);
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 2px 8px rgba(76, 175, 80, 0.3);
}

.logo-text {
  font-size: 26px;
}

.system-info {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.version-tag {
  background: linear-gradient(135deg, #4CAF50, #66BB6A);
  color: white;
  padding: 3px 10px;
  border-radius: 10px;
  font-size: 12px;
  font-weight: 600;
}

.date-tag {
  font-size: 12px;
  color: #999;
}

.header-stats {
  display: flex;
  gap: 32px;
}

.stat-item {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  color: #555;
  font-weight: 500;
}

.stat-icon {
  font-size: 18px;
}

/* ========== 主体内容 ========== */
.main-content {
  max-width: 1600px;
  margin: 0 auto;
  padding: 32px;
  display: grid;
  grid-template-columns: 380px 1fr;
  gap: 32px;
  align-items: start;
}

/* ========== 左侧输入面板 ========== */
.input-panel {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.panel-card {
  background: white;
  border-radius: 20px;
  padding: 28px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.12);
  animation: slideIn 0.5s ease-out;
  border: 1px solid rgba(76, 175, 80, 0.1);
}

.panel-title {
  font-size: 20px;
  font-weight: 700;
  margin-bottom: 24px;
  display: flex;
  align-items: center;
  gap: 10px;
  color: #2c3e50;
  position: relative;
}

.title-line {
  flex: 1;
  height: 2px;
  background: linear-gradient(90deg, #4CAF50 0%, transparent 100%);
}

.icon {
  font-size: 24px;
}

.input-group {
  margin-bottom: 28px;
}

.input-label {
  display: flex;
  justify-content: space-between;
  align-items: baseline;
  margin-bottom: 12px;
  font-weight: 500;
}

.label-text {
  font-size: 15px;
  color: #333;
}

.label-unit {
  font-size: 13px;
  color: #999;
}

.slider-container {
  position: relative;
  display: flex;
  align-items: center;
  gap: 12px;
}

.slider {
  flex: 1;
  height: 8px;
  border-radius: 4px;
  outline: none;
  background: linear-gradient(
    to right,
    #4CAF50 0%,
    #4CAF50 var(--value),
    #e8f5e9 var(--value),
    #e8f5e9 100%
  );
  -webkit-appearance: none;
  cursor: pointer;
  transition: all 0.3s;
}

.slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 22px;
  height: 22px;
  border-radius: 50%;
  background: linear-gradient(135deg, #4CAF50, #66BB6A);
  cursor: pointer;
  box-shadow: 0 3px 12px rgba(76, 175, 80, 0.4);
  transition: all 0.2s;
  border: 3px solid white;
}

.slider::-webkit-slider-thumb:hover {
  transform: scale(1.2);
  box-shadow: 0 4px 16px rgba(76, 175, 80, 0.6);
}

.slider-value {
  min-width: 55px;
  text-align: right;
  font-size: 20px;
  font-weight: 700;
  color: #4CAF50;
}

.slider-marks {
  display: flex;
  justify-content: space-between;
  font-size: 12px;
  color: #999;
  margin-top: 6px;
  padding: 0 2px;
}

.stage-buttons {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 10px;
}

.stage-btn {
  padding: 12px 18px;
  border: 2px solid #e0e0e0;
  background: white;
  border-radius: 10px;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s;
  color: #666;
}

.stage-btn:hover {
  border-color: #4CAF50;
  color: #4CAF50;
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(76, 175, 80, 0.2);
}

.stage-btn.active {
  background: linear-gradient(135deg, #4CAF50, #66BB6A);
  color: white;
  border-color: transparent;
  box-shadow: 0 4px 16px rgba(76, 175, 80, 0.3);
}

.diagnose-btn {
  width: 100%;
  padding: 16px;
  background: linear-gradient(135deg, #4CAF50, #66BB6A);
  color: white;
  border: none;
  border-radius: 12px;
  font-size: 17px;
  font-weight: 600;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  transition: all 0.3s;
  box-shadow: 0 6px 20px rgba(76, 175, 80, 0.3);
  position: relative;
  overflow: hidden;
}

.btn-glow {
  position: absolute;
  top: -50%;
  left: -50%;
  width: 200%;
  height: 200%;
  background: radial-gradient(circle, rgba(255, 255, 255, 0.3) 0%, transparent 70%);
  animation: pulse 2s ease-in-out infinite;
}

@keyframes pulse {
  0%, 100% { opacity: 0.5; transform: scale(0.8); }
  50% { opacity: 1; transform: scale(1.2); }
}

.diagnose-btn:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 24px rgba(76, 175, 80, 0.4);
}

.diagnose-btn:active {
  transform: translateY(0);
}

.btn-icon {
  font-size: 22px;
  position: relative;
  z-index: 1;
}

.btn-arrow {
  font-size: 20px;
  font-weight: bold;
  position: relative;
  z-index: 1;
}

.wheat-display {
  padding: 24px;
}

.mini-title {
  font-size: 17px;
  font-weight: 600;
  margin-bottom: 18px;
  display: flex;
  align-items: center;
  gap: 8px;
  color: #2c3e50;
}

.wheat-images {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 14px;
}

.wheat-img-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  padding: 14px;
  background: linear-gradient(135deg, #f1f8f4, #e8f5e9);
  border-radius: 12px;
  transition: all 0.3s;
  border: 2px solid transparent;
}

.wheat-img-item:hover {
  background: linear-gradient(135deg, #e8f5e9, #c8e6c9);
  transform: translateY(-4px);
  border-color: #4CAF50;
  box-shadow: 0 6px 16px rgba(76, 175, 80, 0.2);
}

.img-wrapper {
  width: 70px;
  height: 70px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: white;
  border-radius: 10px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.img-wrapper img {
  width: 50px;
  height: 50px;
  object-fit: contain;
}

.img-placeholder {
  font-size: 36px;
}

.wheat-img-item span {
  font-size: 13px;
  color: #555;
  font-weight: 500;
}

/* ========== 右侧结果面板 ========== */
.result-panel {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.result-card {
  background: white;
  border-radius: 20px;
  padding: 28px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.12);
  animation: slideIn 0.5s ease-out;
  border: 1px solid rgba(76, 175, 80, 0.1);
}

.diagnosis-result {
  border-left: 5px solid #4CAF50;
}

.diagnosis-result.severe {
  border-left-color: #FF5252;
  background: linear-gradient(135deg, #fff 0%, #ffebee 100%);
}

.diagnosis-result.moderate {
  border-left-color: #FFA726;
  background: linear-gradient(135deg, #fff 0%, #fff3e0 100%);
}

.diagnosis-result.normal {
  border-left-color: #66BB6A;
  background: linear-gradient(135deg, #fff 0%, #f1f8e9 100%);
}

.result-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 18px;
}

.result-title {
  font-size: 22px;
  font-weight: 700;
  display: flex;
  align-items: center;
  gap: 10px;
  color: #2c3e50;
}

.status-icon {
  font-size: 26px;
}

.status-badge {
  padding: 8px 20px;
  border-radius: 24px;
  font-size: 14px;
  font-weight: 600;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.status-badge.severe {
  background: linear-gradient(135deg, #FF5252, #FF8A80);
  color: white;
}

.status-badge.moderate {
  background: linear-gradient(135deg, #FFA726, #FFB74D);
  color: white;
}

.status-badge.normal {
  background: linear-gradient(135deg, #66BB6A, #81C784);
  color: white;
}

.diagnosis-text {
  line-height: 1.9;
  color: #555;
  margin-bottom: 22px;
  font-size: 15px;
}

.diagnosis-details {
  background: rgba(255, 255, 255, 0.7);
  backdrop-filter: blur(10px);
  border-radius: 12px;
  padding: 20px;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
  border: 1px solid rgba(76, 175, 80, 0.15);
}

.detail-item {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.detail-label {
  font-size: 13px;
  color: #999;
  font-weight: 500;
}

.detail-value {
  font-size: 20px;
  font-weight: 700;
  color: #4CAF50;
}

.unit {
  font-size: 13px;
  font-weight: 500;
  color: #999;
}

/* ========== 图表选项卡 ========== */
.chart-tabs {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
  background: white;
  padding: 16px;
  border-radius: 16px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.08);
}

.tab-btn {
  padding: 14px 24px;
  border: 2px solid transparent;
  background: linear-gradient(135deg, #f5f5f5, #fafafa);
  border-radius: 12px;
  font-size: 15px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  color: #666;
}

.tab-btn:hover {
  background: linear-gradient(135deg, #e8f5e9, #f1f8e9);
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(76, 175, 80, 0.15);
}

.tab-btn.active {
  background: linear-gradient(135deg, #4CAF50, #66BB6A);
  color: white;
  border-color: transparent;
  box-shadow: 0 6px 20px rgba(76, 175, 80, 0.3);
}

.tab-icon {
  font-size: 18px;
}

.tab-text {
  font-weight: 600;
}

/* ========== 图表卡片 ========== */
.chart-card {
  min-height: 450px;
}

.chart-wrapper {
  width: 100%;
}

.chart-box {
  width: 100%;
  height: 400px;
}

/* ========== 建议措施 ========== */
.suggestions {
  border-left: 5px solid #FFA726;
}

.suggestion-list {
  list-style: none;
  padding: 0;
}

.suggestion-list li {
  padding: 16px 16px 16px 60px;
  border-bottom: 1px solid #f0f0f0;
  position: relative;
  line-height: 1.7;
  color: #555;
  font-size: 15px;
  transition: all 0.3s;
}

.suggestion-list li:last-child {
  border-bottom: none;
}

.suggestion-list li:hover {
  background: linear-gradient(90deg, #f1f8e9 0%, transparent 100%);
  padding-left: 65px;
}

.suggestion-number {
  position: absolute;
  left: 16px;
  top: 16px;
  width: 32px;
  height: 32px;
  background: linear-gradient(135deg, #4CAF50, #66BB6A);
  color: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 15px;
  font-weight: 700;
  box-shadow: 0 2px 8px rgba(76, 175, 80, 0.3);
}

/* ========== 动画 ========== */
@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* ========== 响应式 ========== */
@media (max-width: 1200px) {
  .main-content {
    grid-template-columns: 1fr;
  }

  .diagnosis-details {
    grid-template-columns: 1fr;
  }

  .chart-tabs {
    grid-template-columns: 1fr;
  }

  .main-title {
    font-size: 32px;
  }
}

@media (max-width: 760px) {
  .main-header {
    padding: 28px 16px 24px;
  }

  .main-title {
    gap: 8px;
    font-size: 25px;
    line-height: 1.4;
  }

  .title-icon {
    font-size: 32px;
  }

  .title-badge {
    padding: 4px 10px;
    font-size: 11px;
  }

  .main-subtitle {
    font-size: 12px;
    letter-spacing: 1px;
  }

  .header {
    padding: 0 16px;
  }

  .header-content {
    min-height: 64px;
    height: auto;
    padding: 12px 0;
  }

  .logo {
    width: 40px;
    height: 40px;
  }

  .header-stats {
    gap: 8px;
  }

  .stat-item {
    font-size: 0;
  }

  .stat-icon {
    font-size: 18px;
  }

  .main-content {
    gap: 18px;
    padding: 18px 14px 28px;
  }

  .panel-card,
  .result-card {
    padding: 20px;
    border-radius: 12px;
  }

  .result-header {
    align-items: flex-start;
    gap: 12px;
  }

  .result-title {
    font-size: 19px;
  }

  .status-badge {
    padding: 6px 12px;
    font-size: 12px;
    white-space: nowrap;
  }

  .chart-tabs {
    grid-template-columns: repeat(3, minmax(0, 1fr));
    gap: 8px;
    padding: 10px;
  }

  .tab-btn {
    min-width: 0;
    padding: 12px 6px;
    gap: 4px;
    font-size: 12px;
  }

  .tab-icon {
    font-size: 15px;
  }

  .chart-card {
    min-height: 360px;
  }

  .chart-box {
    height: 320px;
  }
}

@media (max-width: 520px) {
  .main-title {
    display: block;
    font-size: 22px;
  }

  .title-icon,
  .title-badge {
    display: inline-block;
    margin: 0 4px 8px;
    vertical-align: middle;
  }

  .header-stats {
    display: none;
  }

  .system-info {
    flex-direction: row;
    align-items: center;
  }

  .wheat-images {
    gap: 8px;
  }

  .wheat-img-item {
    min-width: 0;
    padding: 10px 6px;
  }

  .img-wrapper {
    width: 58px;
    height: 58px;
  }

  .img-wrapper img {
    width: 42px;
    height: 42px;
  }

  .suggestion-list li {
    padding: 14px 8px 14px 48px;
    font-size: 14px;
  }

  .suggestion-list li:hover {
    padding-left: 48px;
  }

  .suggestion-number {
    left: 8px;
    top: 14px;
    width: 28px;
    height: 28px;
  }
}
</style>
