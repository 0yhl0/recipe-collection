<template>
  <div class="search-bar">
    <input
      type="text"
      v-model="searchKeyword"
      placeholder="输入食材搜索菜谱（如：鸡蛋、番茄）"
      @input="handleSearch"
    >
    <button @click="resetSearch">重置</button>
  </div>
</template>

<script setup>
import { watch, ref } from 'vue'

const props = defineProps({
  initKeyword: {
    type: String,
    default: ''
  }
})

const emit = defineEmits(['search', 'reset'])

// 搜索关键词响应式数据
const searchKeyword = ref(props.initKeyword)

// 处理搜索
const handleSearch = () => {
  emit('search', searchKeyword.value.trim())
}

// 重置搜索
const resetSearch = () => {
  searchKeyword.value = ''
  emit('reset')
}

// 监听初始关键词变化
watch(() => props.initKeyword, (newVal) => {
  searchKeyword.value = newVal
})
</script>

<style scoped>
.search-bar {
  margin: 20px 0;
  display: flex;
  gap: 10px;
}
.search-bar input {
  flex: 1;
  padding: 10px 15px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
}
.search-bar button {
  padding: 0 20px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
}
.search-bar button:hover {
  background-color: #359469;
}
</style>