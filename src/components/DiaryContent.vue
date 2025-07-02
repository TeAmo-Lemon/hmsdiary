<template>
    <div class="diary-content">
        <div v-if="!selectedDiary" class="welcome-screen">
            <h2>山居札记</h2>
            <p>选择左侧日记开始阅读</p>
        </div>

        <div v-else class="diary-detail">
            <div class="diary-header">
                <button v-if="isMobile" @click="$emit('backToList')" class="back-button">
                    ← 返回列表
                </button>
                <h1 class="diary-title">{{ selectedDiary.title }}</h1>
                <div class="diary-meta">
                    <span class="date">{{ formatDate(selectedDiary.date) }}</span>
                </div>
            </div>

            <div class="diary-body-wrapper">
                <div class="diary-body" v-html="selectedDiary.html"></div>
            </div>
        </div>
    </div>
</template>

<script setup>
defineProps({
    selectedDiary: Object,
    isMobile: Boolean
})

defineEmits(['backToList'])

// 格式化日期
const formatDate = (dateStr) => {
    const date = new Date(dateStr)
    return date.toLocaleDateString('zh-CN', {
        year: 'numeric',
        month: 'long',
        day: 'numeric',
        weekday: 'long'
    })
}
</script>

<style scoped>
.diary-content {
    flex: 1;
    display: flex;
    flex-direction: column;
    background: #fefefe;
    overflow: hidden;
}

.welcome-screen {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    color: #888;
}

.welcome-screen h2 {
    font-size: 24px;
    color: #2f4f2f;
    margin-bottom: 12px;
    font-weight: normal;
    letter-spacing: 2px;
}

.welcome-screen p {
    color: #666;
    font-size: 14px;
}

.diary-detail {
    flex: 1;
    display: flex;
    flex-direction: column;
    height: 100%;
}

.diary-header {
    padding: 35px 40px 25px;
    border-bottom: 1px solid #e0e0e0;
    background: #fafafa;
    position: relative;
}

.back-button {
    position: absolute;
    top: 20px;
    left: 20px;
    background: none;
    border: 1px solid #ccc;
    color: #666;
    padding: 8px 12px;
    border-radius: 4px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.2s ease;
}

.back-button:hover {
    background: #f0f0f0;
    border-color: #999;
}

.back-button:active {
    background: #e8e8e8;
}

.diary-title {
    font-size: 24px;
    color: #2f4f2f;
    margin-bottom: 12px;
    font-weight: normal;
    letter-spacing: 1px;
    line-height: 1.4;
}

.diary-meta {
    display: flex;
    gap: 15px;
    align-items: center;
}

.date {
    font-size: 12px;
    color: #888;
    letter-spacing: 0.5px;
}

.diary-body-wrapper {
    flex: 1;
    overflow-y: auto;
    padding: 0 40px 40px;
}

.diary-body {
    max-width: 680px;
    margin: 0 auto;
    line-height: 1.8;
    font-size: 15px;
    color: #333;
    padding: 30px 0;
}

/* Markdown 样式 */
.diary-body :deep(h1),
.diary-body :deep(h2),
.diary-body :deep(h3),
.diary-body :deep(h4),
.diary-body :deep(h5),
.diary-body :deep(h6) {
    margin: 24px 0 12px 0;
    color: #2f4f2f;
    font-weight: normal;
}

.diary-body :deep(h1) {
    font-size: 20px;
}

.diary-body :deep(h2) {
    font-size: 18px;
}

.diary-body :deep(h3) {
    font-size: 16px;
}

.diary-body :deep(p) {
    margin-bottom: 16px;
    text-align: justify;
}

.diary-body :deep(strong) {
    color: #2f4f2f;
    font-weight: bold;
}

.diary-body :deep(em) {
    font-style: italic;
    color: #555;
}

.diary-body :deep(ul),
.diary-body :deep(ol) {
    margin: 16px 0;
    padding-left: 20px;
}

.diary-body :deep(li) {
    margin-bottom: 6px;
    line-height: 1.6;
}

.diary-body :deep(blockquote) {
    border-left: 3px solid #ccc;
    background: #f8f8f8;
    padding: 12px 16px;
    margin: 20px 0;
    font-style: italic;
    color: #555;
}

.diary-body :deep(code) {
    background: #f5f5f5;
    color: #666;
    padding: 2px 6px;
    border-radius: 3px;
    font-family: 'Courier New', monospace;
    font-size: 13px;
}

.diary-body :deep(pre) {
    background: #f8f8f8;
    border: 1px solid #e0e0e0;
    padding: 16px;
    border-radius: 4px;
    overflow-x: auto;
    margin: 20px 0;
    font-family: 'Courier New', monospace;
}

.diary-body :deep(pre code) {
    background: none;
    padding: 0;
    color: #333;
}

.diary-body :deep(img) {
    max-width: 100%;
    height: auto;
    margin: 20px 0;
}

.diary-body :deep(a) {
    color: #556b2f;
    text-decoration: underline;
}

.diary-body :deep(a:hover) {
    color: #2f4f2f;
}

/* 滚动条样式 */
.diary-body-wrapper::-webkit-scrollbar {
    width: 6px;
}

.diary-body-wrapper::-webkit-scrollbar-track {
    background: #f5f5f5;
}

.diary-body-wrapper::-webkit-scrollbar-thumb {
    background: #ccc;
    border-radius: 3px;
}

.diary-body-wrapper::-webkit-scrollbar-thumb:hover {
    background: #aaa;
}

/* 移动端适配 */
@media (max-width: 768px) {
    .diary-content {
        width: 100%;
        height: 100vh;
        position: absolute;
        top: 0;
        left: 0;
        z-index: 20;
    }

    .welcome-screen {
        padding: 20px;
    }

    .welcome-screen h2 {
        font-size: 20px;
    }

    .diary-header {
        padding: 60px 20px 20px;
        position: sticky;
        top: 0;
        z-index: 1;
        background: #fafafa;
        border-bottom: 1px solid #e0e0e0;
    }

    .back-button {
        top: 15px;
        left: 15px;
        padding: 10px 15px;
        font-size: 15px;
        border-radius: 6px;
    }

    .diary-title {
        font-size: 20px;
        text-align: center;
        margin-top: 10px;
    }

    .diary-meta {
        justify-content: center;
        margin-top: 12px;
    }

    .date {
        font-size: 13px;
    }

    .diary-body-wrapper {
        padding: 0 20px 40px;
        -webkit-overflow-scrolling: touch;
    }

    .diary-body {
        font-size: 16px;
        line-height: 1.7;
        padding: 25px 0;
    }

    .diary-body :deep(h1) {
        font-size: 18px;
    }

    .diary-body :deep(h2) {
        font-size: 17px;
    }

    .diary-body :deep(h3) {
        font-size: 16px;
    }

    .diary-body :deep(p) {
        margin-bottom: 18px;
    }

    .diary-body :deep(blockquote) {
        margin: 24px 0;
        padding: 15px 18px;
    }

    .diary-body :deep(pre) {
        padding: 18px;
        margin: 24px 0;
        font-size: 14px;
    }

    .diary-body :deep(code) {
        font-size: 14px;
        padding: 3px 8px;
    }

    /* 隐藏桌面端滚动条 */
    .diary-body-wrapper::-webkit-scrollbar {
        display: none;
    }
}

@media (max-width: 480px) {
    .diary-header {
        padding: 55px 15px 18px;
    }

    .back-button {
        top: 12px;
        left: 12px;
        padding: 8px 12px;
        font-size: 14px;
    }

    .diary-title {
        font-size: 18px;
    }

    .diary-body-wrapper {
        padding: 0 15px 30px;
    }

    .diary-body {
        font-size: 15px;
        padding: 20px 0;
    }
}
</style>