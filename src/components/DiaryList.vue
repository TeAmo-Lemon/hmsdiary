<template>
    <div class="diary-list">
        <div class="list-header">
            <h3>时光记录</h3>
            <div class="diary-count">{{ diaries.length }} 篇</div>
        </div>

        <div v-if="diaries.length === 0" class="empty-state">
            <p>暂无记录</p>
            <small>将 .md 文件放入 src/diaries/ 开始记录</small>
        </div>

        <div v-else class="diary-scroll">
            <div v-for="diary in diaries" :key="diary.id" @click="$emit('selectDiary', diary)"
                :class="{ active: selectedDiaryId === diary.id }" class="diary-item">
                <div class="diary-date">{{ formatDate(diary.date) }}</div>
                <div class="diary-title">{{ diary.title }}</div>
                <div class="diary-preview">{{ getPreview(diary.content) }}</div>
            </div>
        </div>
    </div>
</template>

<script setup>
defineProps({
    diaries: Array,
    selectedDiaryId: String
})

defineEmits(['selectDiary'])

// 格式化日期
const formatDate = (dateStr) => {
    const date = new Date(dateStr)
    const today = new Date()
    const diffTime = today - date
    const diffDays = Math.floor(diffTime / (1000 * 60 * 60 * 24))

    if (diffDays === 0) return '今日'
    if (diffDays === 1) return '昨日'
    if (diffDays < 30) return `${diffDays} 日前`

    return date.toLocaleDateString('zh-CN', {
        month: 'long',
        day: 'numeric'
    })
}

// 获取内容预览
const getPreview = (content) => {
    const text = content
        .replace(/^#+\s/gm, '')
        .replace(/\*\*(.*?)\*\*/g, '$1')
        .replace(/\*(.*?)\*/g, '$1')
        .replace(/`(.*?)`/g, '$1')
        .replace(/\n/g, ' ')
        .trim()

    return text.length > 40 ? text.substring(0, 40) + '...' : text
}
</script>

<style scoped>
.diary-list {
    width: 280px;
    background: #f9f9f9;
    border-right: 1px solid #ddd;
    display: flex;
    flex-direction: column;
    height: 100%;
}

.list-header {
    padding: 25px 20px 20px;
    border-bottom: 1px solid #e0e0e0;
    background: #f5f5f5;
}

.list-header h3 {
    font-size: 16px;
    font-weight: normal;
    color: #2f4f2f;
    margin-bottom: 5px;
    letter-spacing: 1px;
}

.diary-count {
    font-size: 12px;
    color: #888;
}

.empty-state {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 40px 20px;
    color: #888;
    text-align: center;
}

.empty-state p {
    font-size: 14px;
    margin-bottom: 8px;
    color: #666;
}

.empty-state small {
    font-size: 11px;
    color: #aaa;
    line-height: 1.4;
}

.diary-scroll {
    flex: 1;
    overflow-y: auto;
    padding: 15px 0;
}

.diary-item {
    padding: 15px 20px;
    cursor: pointer;
    border-bottom: 1px solid #f0f0f0;
    transition: background-color 0.2s ease;
}

.diary-item:hover {
    background: #f0f0f0;
}

.diary-item.active {
    background: #e8f5e8;
    border-right: 3px solid #556b2f;
}

.diary-date {
    font-size: 11px;
    color: #888;
    margin-bottom: 5px;
    letter-spacing: 0.5px;
}

.diary-title {
    font-size: 14px;
    font-weight: normal;
    color: #2f4f2f;
    margin-bottom: 6px;
    line-height: 1.3;
}

.diary-preview {
    font-size: 12px;
    color: #666;
    line-height: 1.4;
    opacity: 0.8;
}

/* 滚动条样式 */
.diary-scroll::-webkit-scrollbar {
    width: 4px;
}

.diary-scroll::-webkit-scrollbar-track {
    background: #f5f5f5;
}

.diary-scroll::-webkit-scrollbar-thumb {
    background: #ccc;
    border-radius: 2px;
}

.diary-scroll::-webkit-scrollbar-thumb:hover {
    background: #aaa;
}

/* 移动端适配 */
@media (max-width: 768px) {
    .diary-list {
        width: 100%;
        height: 100vh;
        border-right: none;
        position: absolute;
        top: 0;
        left: 0;
        z-index: 10;
    }

    .list-header {
        padding: 20px 15px 15px;
        position: sticky;
        top: 0;
        z-index: 1;
    }

    .list-header h3 {
        font-size: 18px;
    }

    .diary-scroll {
        padding: 10px 0 20px;
        -webkit-overflow-scrolling: touch;
    }

    .diary-item {
        padding: 18px 15px;
        border-bottom: 1px solid #e8e8e8;
    }

    .diary-item:active {
        background: #f0f0f0;
    }

    .diary-item.active {
        border-right: none;
        border-left: 4px solid #556b2f;
    }

    .diary-date {
        font-size: 12px;
        margin-bottom: 6px;
    }

    .diary-title {
        font-size: 16px;
        margin-bottom: 8px;
        line-height: 1.4;
    }

    .diary-preview {
        font-size: 13px;
        line-height: 1.5;
    }

    /* 隐藏桌面端滚动条，使用原生移动端滚动 */
    .diary-scroll::-webkit-scrollbar {
        display: none;
    }
}
</style>