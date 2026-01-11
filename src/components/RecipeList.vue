<template>
  <div class="recipe-list">
    <div class="list-header">
      <h2>{{ isFavorite ? '我的收藏' : '全部菜谱' }}</h2>
      <button 
        @click="toggleFavoriteView"
        :class="{ active: isFavorite }"
      >
        {{ isFavorite ? '查看全部菜谱' : '查看收藏' }}
      </button>
    </div>
    
    <div class="card-container">
      <!-- 无数据提示 -->
      <div class="empty-tip" v-if="filteredRecipes.length === 0">
        {{ isFavorite ? '暂无收藏的菜谱' : '未找到相关菜谱' }}
      </div>
      
      <!-- 菜谱卡片 -->
      <div 
        class="recipe-card"
        v-for="recipe in filteredRecipes"
        :key="recipe.id"
        @click="goToDetail(recipe.id)"
      >
        <img :src="recipe.cover" alt="recipe.title" class="card-cover">
        <div class="card-content">
          <h3 class="card-title">{{ recipe.title }}</h3>
          <div class="card-ingredients">
            食材：{{ recipe.ingredients.join('、') }}
          </div>
          <button 
            class="collect-btn"
            @click.stop="toggleCollect(recipe.id)"
            :class="{ collected: collectedIds.includes(recipe.id) }"
          >
            {{ collectedIds.includes(recipe.id) ? '取消收藏' : '加入收藏' }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  recipes: {
    type: Array,
    required: true
  },
  searchKeyword: {
    type: String,
    default: ''
  },
  collectedIds: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['goDetail', 'toggleCollect'])

// 是否显示收藏视图
const isFavorite = ref(false)

// 筛选后的菜谱列表
const filteredRecipes = computed(() => {
  let list = [...props.recipes]
  
  // 1. 先过滤收藏/全部视图
  if (isFavorite.value) {
    list = list.filter(recipe => props.collectedIds.includes(recipe.id))
  }
  
  // 2. 再过滤搜索关键词
  if (props.searchKeyword) {
    const keyword = props.searchKeyword.toLowerCase()
    list = list.filter(recipe => {
      // 匹配标题或食材
      return recipe.title.toLowerCase().includes(keyword) || 
             recipe.ingredients.some(ing => ing.toLowerCase().includes(keyword))
    })
  }
  
  return list
})

// 切换收藏视图
const toggleFavoriteView = () => {
  isFavorite.value = !isFavorite.value
}

// 跳转到详情页
const goToDetail = (id) => {
  emit('goDetail', id)
}

// 收藏/取消收藏
const toggleCollect = (id) => {
  emit('toggleCollect', id)
}
</script>

<style scoped>
.recipe-list {
  margin-top: 20px;
}
.list-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}
.list-header button {
  padding: 8px 16px;
  border: 1px solid #42b983;
  background-color: white;
  color: #42b983;
  border-radius: 4px;
  cursor: pointer;
}
.list-header button.active {
  background-color: #42b983;
  color: white;
}
.card-container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
}
.empty-tip {
  grid-column: 1 / -1;
  text-align: center;
  padding: 40px;
  color: #999;
  font-size: 18px;
}
.recipe-card {
  background-color: white;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  cursor: pointer;
  transition: transform 0.2s;
}
.recipe-card:hover {
  transform: translateY(-5px);
}
.card-cover {
  width: 100%;
  height: 200px;
  object-fit: cover;
}
.card-content {
  padding: 15px;
}
.card-title {
  font-size: 18px;
  margin-bottom: 10px;
  color: #333;
}
.card-ingredients {
  font-size: 14px;
  color: #666;
  margin-bottom: 15px;
  line-height: 1.5;
}
.collect-btn {
  padding: 6px 12px;
  border: 1px solid #ff6700;
  background-color: white;
  color: #ff6700;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
}
.collect-btn.collected {
  background-color: #ff6700;
  color: white;
}
</style>