<template>
  <div class="recipe-detail" v-if="recipe">
    <button class="back-btn" @click="goBack">← 返回列表</button>
    
    <div class="detail-header">
      <img :src="recipe.cover" alt="recipe.title" class="detail-cover">
      <div class="detail-title-wrap">
        <h1 class="detail-title">{{ recipe.title }}</h1>
        <button 
          class="collect-btn"
          @click="toggleCollect"
          :class="{ collected: isCollected }"
        >
          {{ isCollected ? '取消收藏' : '加入收藏' }}
        </button>
      </div>
    </div>
    
    <div class="detail-content">
      <div class="ingredients-section">
        <h3>食材</h3>
        <ul class="ingredients-list">
          <li v-for="(ing, index) in recipe.ingredients" :key="index">
            {{ ing }}
          </li>
        </ul>
      </div>
      
      <div class="steps-section">
        <h3>制作步骤</h3>
        <ol class="steps-list">
          <li v-for="(step, index) in recipe.steps" :key="index">
            <span class="step-num">{{ index + 1 }}</span>
            <span class="step-text">{{ step }}</span>
          </li>
        </ol>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  recipe: {
    type: Object,
    default: null
  },
  collectedIds: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['goBack', 'toggleCollect'])

// 是否已收藏
const isCollected = computed(() => {
  return props.recipe && props.collectedIds.includes(props.recipe.id)
})

// 返回列表
const goBack = () => {
  emit('goBack')
}

// 收藏/取消收藏
const toggleCollect = () => {
  if (props.recipe) {
    emit('toggleCollect', props.recipe.id)
  }
}
</script>

<style scoped>
.recipe-detail {
  background-color: white;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}
.back-btn {
  padding: 8px 16px;
  border: none;
  background-color: #f5f5f5;
  color: #333;
  border-radius: 4px;
  cursor: pointer;
  margin-bottom: 20px;
}
.detail-header {
  display: flex;
  gap: 20px;
  margin-bottom: 30px;
}
.detail-cover {
  width: 300px;
  height: 200px;
  object-fit: cover;
  border-radius: 8px;
}
.detail-title-wrap {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}
.detail-title {
  font-size: 24px;
  color: #333;
}
.collect-btn {
  align-self: flex-start;
  padding: 8px 16px;
  border: 1px solid #ff6700;
  background-color: white;
  color: #ff6700;
  border-radius: 4px;
  cursor: pointer;
}
.collect-btn.collected {
  background-color: #ff6700;
  color: white;
}
.detail-content {
  display: grid;
  grid-template-columns: 1fr 2fr;
  gap: 30px;
}
.ingredients-section, .steps-section {
  padding: 10px;
}
h3 {
  font-size: 18px;
  color: #333;
  margin-bottom: 15px;
  padding-bottom: 8px;
  border-bottom: 1px solid #eee;
}
.ingredients-list {
  list-style: none;
}
.ingredients-list li {
  padding: 8px 0;
  border-bottom: 1px dashed #eee;
  color: #666;
}
.steps-list {
  list-style: none;
  counter-reset: step-counter;
}
.steps-list li {
  position: relative;
  padding: 15px 0 15px 40px;
  border-bottom: 1px dashed #eee;
  color: #666;
  line-height: 1.6;
}
.steps-list li:before {
  content: counter(step-counter);
  counter-increment: step-counter;
  position: absolute;
  left: 0;
  top: 15px;
  width: 30px;
  height: 30px;
  background-color: #42b983;
  color: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 14px;
}
</style>