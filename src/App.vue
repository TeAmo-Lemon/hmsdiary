<script setup>
import { ref, onMounted } from 'vue'
import { marked } from 'marked'
import DiaryList from '@/components/DiaryList.vue'
import DiaryContent from '@/components/DiaryContent.vue'

const diaries = ref([])
const selectedDiary = ref(null)
const isMobile = ref(false)
const showList = ref(true) // 在移动端控制显示列表还是内容

// 检测是否为移动设备
const checkMobile = () => {
  isMobile.value = window.innerWidth <= 768
  if (!isMobile.value) {
    showList.value = true // 桌面端始终显示列表
  }
}

// 配置 marked 选项
marked.setOptions({
  breaks: true,
  gfm: true
})

// 动态导入所有日记文件
const loadDiaries = async () => {
  // 使用 Vite 的 import.meta.glob 来动态导入所有 md 文件
  const diaryModules = import.meta.glob('./diaries/*.md', { as: 'raw' })

  const diaryList = []

  for (const path in diaryModules) {
    try {
      const content = await diaryModules[path]()
      const filename = path.split('/').pop()
      const match = filename.match(/^(\d{4}-\d{2}-\d{2})-(.+)\.md$/)

      if (match) {
        const [, date, titleSlug] = match
        const title = titleSlug.replace(/-/g, ' ')

        diaryList.push({
          id: filename,
          title,
          date,
          content,
          html: marked(content),
          dateObj: new Date(date) // 添加日期对象用于准确排序
        })
      }
    } catch (error) {
      console.error(`加载日记文件 ${path} 失败:`, error)
    }
  }

  // 按日期排序（最新的在前面）
  diaryList.sort((a, b) => b.dateObj - a.dateObj)
  diaries.value = diaryList

  // 自动选择最新的日记
  if (diaryList.length > 0) {
    selectedDiary.value = diaryList[0]
  }
}

const selectDiary = (diary) => {
  selectedDiary.value = diary
  // 在移动端选择日记后显示内容
  if (isMobile.value) {
    showList.value = false
  }
}

// 返回列表（移动端）
const backToList = () => {
  showList.value = true
}

onMounted(() => {
  loadDiaries()
  checkMobile()
  window.addEventListener('resize', checkMobile)
})
</script>

<template>
  <div class="app">
    <div class="app-header">
      <h1 class="site-title">山居札记</h1>
      <p class="site-subtitle">远山如黛，心如止水</p>
    </div>
    <div class="app-content">
      <DiaryList :diaries="diaries" :selectedDiaryId="selectedDiary?.id"
        :class="{ 'mobile-hidden': isMobile && !showList }" @selectDiary="selectDiary" />
      <DiaryContent :selectedDiary="selectedDiary" :isMobile="isMobile"
        :class="{ 'mobile-hidden': isMobile && showList }" @backToList="backToList" />
    </div>
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Times New Roman', 'SimSun', serif;
  background: #f5f5dc;
  min-height: 100vh;
  color: #2f4f2f;
}

.app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.app-header {
  background: #2f4f2f;
  padding: 30px 40px;
  text-align: center;
  border-bottom: 3px solid #556b2f;
}

.site-title {
  font-size: 28px;
  font-weight: normal;
  color: #f5f5dc;
  margin-bottom: 8px;
  letter-spacing: 4px;
}

.site-subtitle {
  font-size: 14px;
  color: #d2b48c;
  font-weight: normal;
  letter-spacing: 2px;
  opacity: 0.8;
}

.app-content {
  display: flex;
  flex: 1;
  background: #fefefe;
  box-shadow: inset 0 1px 3px rgba(0, 0, 0, 0.1);
}

/* 移动端适配 */
@media (max-width: 768px) {
  .app-header {
    padding: 20px 20px;
  }

  .site-title {
    font-size: 22px;
    letter-spacing: 2px;
  }

  .site-subtitle {
    font-size: 12px;
    letter-spacing: 1px;
  }

  .app-content {
    position: relative;
  }

  .mobile-hidden {
    display: none !important;
  }
}
</style>
