<script setup>
import { ref, computed, onMounted } from 'vue'

// 状态
const currentView = ref('tasks') // tasks, courses, my
const tasks = ref([])
const courses = ref([])
const myTasks = ref([])
const mySkills = ref([])

// 从 localStorage 加载
const loadData = () => {
  const savedTasks = localStorage.getItem('rural-tasks')
  const savedCourses = localStorage.getItem('rural-courses')
  const savedMyTasks = localStorage.getItem('rural-my-tasks')
  const savedMySkills = localStorage.getItem('rural-my-skills')

  if (savedTasks) tasks.value = JSON.parse(savedTasks)
  else {
    tasks.value = [
      {
        id: 1,
        title: '数据标注-图片分类',
        description: '将图片按类别进行分类标注，适合新手',
        price: 0.5,
        priceUnit: '元/张',
        total: 1000,
        remaining: 650,
        difficulty: '简单',
        skills: ['数据标注'],
        deadline: '3天',
        employer: '某AI公司'
      },
      {
        id: 2,
        title: '录音采集-方言语音',
        description: '录制家乡方言语音，用于语音识别训练',
        price: 2,
        priceUnit: '元/条',
        total: 500,
        remaining: 320,
        difficulty: '简单',
        skills: ['语音采集'],
        deadline: '5天',
        employer: '某语音公司'
      },
      {
        id: 3,
        title: '文字转录-会议记录',
        description: '将音频会议内容转录为文字',
        price: 30,
        priceUnit: '元/小时',
        total: 50,
        remaining: 38,
        difficulty: '中等',
        skills: ['文字录入', '普通话'],
        deadline: '7天',
        employer: '某咨询公司'
      },
      {
        id: 4,
        title: '问卷调查-农村消费',
        description: '完成农村消费习惯调查问卷',
        price: 5,
        priceUnit: '元/份',
        total: 200,
        remaining: 156,
        difficulty: '简单',
        skills: ['问卷调查'],
        deadline: '10天',
        employer: '某研究院'
      },
      {
        id: 5,
        title: '视频剪辑-短视频',
        description: '按要求剪辑短视频素材',
        price: 50,
        priceUnit: '元/条',
        total: 20,
        remaining: 15,
        difficulty: '中等',
        skills: ['视频剪辑'],
        deadline: '7天',
        employer: '某MCN机构'
      },
      {
        id: 6,
        title: '客服外包-在线回复',
        description: '在线客服，回复客户咨询',
        price: 15,
        priceUnit: '元/小时',
        total: 100,
        remaining: 80,
        difficulty: '中等',
        skills: ['客服', '打字'],
        deadline: '长期',
        employer: '某电商平台'
      }
    ]
    saveData()
  }

  if (savedCourses) courses.value = JSON.parse(savedCourses)
  else {
    courses.value = [
      {
        id: 1,
        title: '数据标注入门',
        description: '学习数据标注的基本方法和技巧',
        duration: '2小时',
        lessons: 8,
        enrolled: 1256,
        skills: ['数据标注'],
        progress: 0,
        image: '📊'
      },
      {
        id: 2,
        title: '语音采集技巧',
        description: '如何高质量采集语音数据',
        duration: '1小时',
        lessons: 5,
        enrolled: 892,
        skills: ['语音采集'],
        progress: 0,
        image: '🎤'
      },
      {
        id: 3,
        title: '打字速度提升',
        description: '从入门到每分钟80字',
        duration: '3小时',
        lessons: 12,
        enrolled: 2341,
        skills: ['打字'],
        progress: 0,
        image: '⌨️'
      },
      {
        id: 4,
        title: '视频剪辑基础',
        description: '剪映/PR入门教程',
        duration: '4小时',
        lessons: 15,
        enrolled: 1567,
        skills: ['视频剪辑'],
        progress: 0,
        image: '🎬'
      },
      {
        id: 5,
        title: '客服沟通技巧',
        description: '提升客户满意度的话术',
        duration: '2小时',
        lessons: 10,
        enrolled: 945,
        skills: ['客服'],
        progress: 0,
        image: '💬'
      }
    ]
    saveData()
  }

  if (savedMyTasks) myTasks.value = JSON.parse(savedMyTasks)
  if (savedMySkills) mySkills.value = JSON.parse(savedMySkills)
}

const saveData = () => {
  localStorage.setItem('rural-tasks', JSON.stringify(tasks.value))
  localStorage.setItem('rural-courses', JSON.stringify(courses.value))
  localStorage.setItem('rural-my-tasks', JSON.stringify(myTasks.value))
  localStorage.setItem('rural-my-skills', JSON.stringify(mySkills.value))
}

// 推荐任务（基于已有技能）
const recommendedTasks = computed(() => {
  if (mySkills.value.length === 0) return tasks.value.slice(0, 3)
  return tasks.value.filter(t => t.skills.some(s => mySkills.value.includes(s))).slice(0, 3)
})

// 接单
const acceptTask = (task) => {
  myTasks.value.push({
    ...task,
    acceptedAt: Date.now(),
    status: '进行中',
    progress: 0
  })
  task.remaining--
  saveData()
}

// 学习课程
const startCourse = (course) => {
  course.progress = 10
  // 自动添加技能
  course.skills.forEach(s => {
    if (!mySkills.value.includes(s)) {
      mySkills.value.push(s)
    }
  })
  saveData()
}

// 完成任务
const completeTask = (myTask) => {
  myTask.status = '已完成'
  myTask.progress = 100
  saveData()
}

// 计算收益
const totalEarnings = computed(() => {
  return myTasks.value
    .filter(t => t.status === '已完成')
    .reduce((sum, t) => sum + t.price * (t.progress || 1), 0)
})

const formatTime = (timestamp) => {
  const date = new Date(timestamp)
  return `${date.getMonth() + 1}/${date.getDate()}`
}

onMounted(loadData)
</script>

<template>
  <div class="min-h-screen bg-green-50">
    <!-- 底部导航 -->
    <nav class="fixed bottom-0 left-0 right-0 bg-white border-t border-gray-100 z-10">
      <div class="max-w-lg mx-auto flex">
        <button
          @click="currentView = 'tasks'"
          :class="['flex-1 py-3 text-center', currentView === 'tasks' ? 'text-green-500' : 'text-gray-400']"
        >
          <div class="text-xl">📋</div>
          <div class="text-xs mt-1">任务大厅</div>
        </button>
        <button
          @click="currentView = 'courses'"
          :class="['flex-1 py-3 text-center', currentView === 'courses' ? 'text-green-500' : 'text-gray-400']"
        >
          <div class="text-xl">📚</div>
          <div class="text-xs mt-1">技能课程</div>
        </button>
        <button
          @click="currentView = 'my'"
          :class="['flex-1 py-3 text-center', currentView === 'my' ? 'text-green-500' : 'text-gray-400']"
        >
          <div class="text-xl">👤</div>
          <div class="text-xs mt-1">我的</div>
        </button>
      </div>
    </nav>

    <!-- 任务大厅 -->
    <div v-if="currentView === 'tasks'" class="max-w-lg mx-auto px-4 py-4 pb-20">
      <h1 class="text-xl font-bold text-gray-800 mb-4">🌾 任务大厅</h1>

      <!-- 推荐任务 -->
      <div v-if="mySkills.length > 0" class="mb-4">
        <h2 class="text-sm font-medium text-gray-600 mb-2">为你推荐</h2>
        <div class="space-y-2">
          <div
            v-for="task in recommendedTasks"
            :key="task.id"
            class="bg-white rounded-xl p-4 shadow-sm"
          >
            <div class="flex justify-between items-start mb-2">
              <h3 class="font-medium text-gray-800">{{ task.title }}</h3>
              <span :class="[
                'text-xs px-2 py-1 rounded-full',
                task.difficulty === '简单' ? 'bg-green-50 text-green-500' : 'bg-yellow-50 text-yellow-600'
              ]">{{ task.difficulty }}</span>
            </div>
            <p class="text-sm text-gray-500 mb-2">{{ task.description }}</p>
            <div class="flex items-center justify-between">
              <div class="text-green-500 font-medium">
                ¥{{ task.price }}<span class="text-xs text-gray-400">{{ task.priceUnit }}</span>
              </div>
              <button
                @click="acceptTask(task)"
                class="bg-green-500 text-white px-4 py-1.5 rounded-full text-sm hover:bg-green-600 transition"
              >
                接单
              </button>
            </div>
          </div>
        </div>
      </div>

      <!-- 所有任务 -->
      <h2 class="text-sm font-medium text-gray-600 mb-2">全部任务</h2>
      <div class="space-y-2">
        <div
          v-for="task in tasks"
          :key="task.id"
          class="bg-white rounded-xl p-4 shadow-sm"
        >
          <div class="flex justify-between items-start mb-2">
            <h3 class="font-medium text-gray-800">{{ task.title }}</h3>
            <span :class="[
              'text-xs px-2 py-1 rounded-full',
              task.difficulty === '简单' ? 'bg-green-50 text-green-500' : 'bg-yellow-50 text-yellow-600'
            ]">{{ task.difficulty }}</span>
          </div>
          <p class="text-sm text-gray-500 mb-2">{{ task.description }}</p>
          <div class="flex flex-wrap gap-1 mb-2">
            <span
              v-for="skill in task.skills"
              :key="skill"
              class="text-xs bg-gray-50 text-gray-500 px-2 py-0.5 rounded"
            >
              {{ skill }}
            </span>
          </div>
          <div class="flex items-center justify-between text-sm">
            <div>
              <span class="text-green-500 font-medium">¥{{ task.price }}</span>
              <span class="text-gray-400 text-xs">{{ task.priceUnit }}</span>
              <span class="text-gray-300 mx-2">·</span>
              <span class="text-gray-400 text-xs">剩余 {{ task.remaining }}/{{ task.total }}</span>
            </div>
            <button
              @click="acceptTask(task)"
              :disabled="task.remaining === 0"
              :class="[
                'px-4 py-1.5 rounded-full text-sm transition',
                task.remaining > 0
                  ? 'bg-green-500 text-white hover:bg-green-600'
                  : 'bg-gray-100 text-gray-400 cursor-not-allowed'
              ]"
            >
              {{ task.remaining > 0 ? '接单' : '已抢光' }}
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- 技能课程 -->
    <div v-else-if="currentView === 'courses'" class="max-w-lg mx-auto px-4 py-4 pb-20">
      <h1 class="text-xl font-bold text-gray-800 mb-4">📚 技能课程</h1>

      <div class="space-y-3">
        <div
          v-for="course in courses"
          :key="course.id"
          class="bg-white rounded-xl p-4 shadow-sm"
        >
          <div class="flex gap-3">
            <div class="text-3xl">{{ course.image }}</div>
            <div class="flex-1">
              <h3 class="font-medium text-gray-800 mb-1">{{ course.title }}</h3>
              <p class="text-sm text-gray-500 mb-2">{{ course.description }}</p>
              <div class="flex items-center gap-3 text-xs text-gray-400">
                <span>⏱ {{ course.duration }}</span>
                <span>📖 {{ course.lessons }}节</span>
                <span>👥 {{ course.enrolled }}人学习</span>
              </div>
              <div class="flex flex-wrap gap-1 mt-2">
                <span
                  v-for="skill in course.skills"
                  :key="skill"
                  class="text-xs bg-green-50 text-green-600 px-2 py-0.5 rounded"
                >
                  {{ skill }}
                </span>
              </div>
            </div>
          </div>
          <div class="mt-3 pt-3 border-t border-gray-50">
            <div v-if="course.progress > 0" class="mb-2">
              <div class="flex justify-between text-xs text-gray-400 mb-1">
                <span>学习进度</span>
                <span>{{ course.progress }}%</span>
              </div>
              <div class="h-1.5 bg-gray-100 rounded-full overflow-hidden">
                <div class="h-full bg-green-500 rounded-full" :style="{ width: course.progress + '%' }"></div>
              </div>
            </div>
            <button
              @click="startCourse(course)"
              class="w-full py-2 rounded-lg text-sm font-medium transition"
              :class="course.progress > 0 ? 'bg-green-100 text-green-600' : 'bg-green-500 text-white hover:bg-green-600'"
            >
              {{ course.progress > 0 ? '继续学习' : '开始学习' }}
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- 我的 -->
    <div v-else-if="currentView === 'my'" class="max-w-lg mx-auto px-4 py-4 pb-20">
      <h1 class="text-xl font-bold text-gray-800 mb-4">👤 我的</h1>

      <!-- 收益统计 -->
      <div class="bg-white rounded-xl p-4 shadow-sm mb-4">
        <div class="text-center">
          <div class="text-3xl font-bold text-green-500 mb-1">¥{{ totalEarnings.toFixed(2) }}</div>
          <div class="text-sm text-gray-400">累计收益</div>
        </div>
        <div class="grid grid-cols-3 gap-4 mt-4 pt-4 border-t border-gray-50">
          <div class="text-center">
            <div class="text-xl font-bold text-gray-800">{{ myTasks.filter(t => t.status === '进行中').length }}</div>
            <div class="text-xs text-gray-400">进行中</div>
          </div>
          <div class="text-center">
            <div class="text-xl font-bold text-gray-800">{{ myTasks.filter(t => t.status === '已完成').length }}</div>
            <div class="text-xs text-gray-400">已完成</div>
          </div>
          <div class="text-center">
            <div class="text-xl font-bold text-gray-800">{{ mySkills.length }}</div>
            <div class="text-xs text-gray-400">已获技能</div>
          </div>
        </div>
      </div>

      <!-- 我的技能 -->
      <div class="bg-white rounded-xl p-4 shadow-sm mb-4">
        <h2 class="font-medium text-gray-800 mb-3">我的技能</h2>
        <div v-if="mySkills.length > 0" class="flex flex-wrap gap-2">
          <span
            v-for="skill in mySkills"
            :key="skill"
            class="bg-green-50 text-green-600 px-3 py-1 rounded-full text-sm"
          >
            ✓ {{ skill }}
          </span>
        </div>
        <div v-else class="text-gray-400 text-sm text-center py-4">
          还没有技能，去学习课程吧
        </div>
      </div>

      <!-- 我的任务 -->
      <div class="bg-white rounded-xl p-4 shadow-sm">
        <h2 class="font-medium text-gray-800 mb-3">我的任务</h2>
        <div v-if="myTasks.length > 0" class="space-y-3">
          <div
            v-for="myTask in myTasks"
            :key="myTask.id + myTask.acceptedAt"
            class="border-b border-gray-50 pb-3 last:border-0"
          >
            <div class="flex justify-between items-start mb-2">
              <div>
                <h3 class="font-medium text-gray-800">{{ myTask.title }}</h3>
                <p class="text-xs text-gray-400">接单时间: {{ formatTime(myTask.acceptedAt) }}</p>
              </div>
              <span
                :class="[
                  'text-xs px-2 py-1 rounded-full',
                  myTask.status === '进行中' ? 'bg-yellow-50 text-yellow-600' : 'bg-green-50 text-green-500'
                ]"
              >
                {{ myTask.status }}
              </span>
            </div>
            <div v-if="myTask.status === '进行中'" class="flex items-center justify-between">
              <span class="text-green-500 font-medium">¥{{ myTask.price }}{{ myTask.priceUnit }}</span>
              <button
                @click="completeTask(myTask)"
                class="bg-green-500 text-white px-4 py-1.5 rounded-full text-sm hover:bg-green-600 transition"
              >
                完成任务
              </button>
            </div>
            <div v-else class="text-green-500 font-medium">
              已收益: ¥{{ myTask.price }}
            </div>
          </div>
        </div>
        <div v-else class="text-gray-400 text-sm text-center py-4">
          还没有接单，去任务大厅看看吧
        </div>
      </div>
    </div>
  </div>
</template>
