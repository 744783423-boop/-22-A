<script setup>
import { ref, reactive, computed, onMounted, nextTick } from 'vue'

const sidebarItems = [
  { key: 'strategy', label: '策略输出功能' }
]

const topTabs = [
  '投放',
  '直播',
  '达播',
  '国补',
  '百补',
  '客服',
  '营销',
  'CRM/Affiliate',
  '整合汇报组专用'
]

const activeSidebar = ref('strategy')
const activeTabIndex = ref(0)

const uploadedImageUrl = ref('')
const isAnalyzing = ref(false)

const messages = ref([])
const chatInput = ref('')
const displayPanelRef = ref(null)

function handleImageChange(event) {
  const file = event.target.files && event.target.files[0]
  if (!file) return
  const reader = new FileReader()
  reader.onload = () => {
    uploadedImageUrl.value = String(reader.result || '')
  }
  reader.readAsDataURL(file)
}

async function startAnalyze() {
  if (!uploadedImageUrl.value || isAnalyzing.value) return
  isAnalyzing.value = true
  appendMessage('系统', `正在分析 ${topTabs[activeTabIndex.value]} 图片内容...`)

  // Simulate AI analysis streaming output
  const chunks = [
    '识别到关键元素：产品、价格标签、核心卖点。',
    '建议优化标题与次级卖点排版，提高可读性。',
    '话术建议：突出利益点、制造稀缺、加入行动指令。'
  ]
  for (const chunk of chunks) {
    await sleep(700)
    appendMessage('AI', chunk)
  }
  isAnalyzing.value = false
}

function sleep(ms) { return new Promise(r => setTimeout(r, ms)) }

function appendMessage(role, content) {
  messages.value.push({ id: Date.now() + Math.random(), role, content })
  nextTick(() => {
    const el = displayPanelRef.value
    if (el) {
      el.scrollTop = el.scrollHeight
    }
  })
}

async function sendChat() {
  const text = chatInput.value.trim()
  if (!text) return
  appendMessage('我', text)
  chatInput.value = ''
  // Simulate AI reply streaming
  const reply = `基于“${topTabs[activeTabIndex.value]}”场景，建议：`
  appendMessage('AI', reply)
  const bullets = [
    '开场强调核心利益点',
    '中段用数字化证据增强信任',
    '结尾给出明确行动指令'
  ]
  for (const b of bullets) {
    await sleep(500)
    appendMessage('AI', `- ${b}`)
  }
}
</script>

<template>
  <div class="app-root">
    <header class="app-header">
      <div class="brand">鹏宝PPT</div>
    </header>

    <div class="app-body">
      <aside class="sidebar">
        <nav>
          <ul>
            <li
              v-for="item in sidebarItems"
              :key="item.key"
              :class="{ active: activeSidebar === item.key }"
              @click="activeSidebar = item.key"
            >
              {{ item.label }}
            </li>
          </ul>
        </nav>
      </aside>

      <main class="content">
        <div class="top-tabs">
          <button
            v-for="(tab, idx) in topTabs"
            :key="tab"
            :class="['tab', { active: activeTabIndex === idx }]"
            @click="activeTabIndex = idx"
          >
            {{ tab }}
          </button>
        </div>

        <div class="main-panels">
          <section class="left-panel">
            <div class="uploader">
              <label class="upload-box">
                <input type="file" accept="image/*" @change="handleImageChange" />
                <span v-if="!uploadedImageUrl">点击上传图片</span>
                <img v-else :src="uploadedImageUrl" alt="预览" />
              </label>
              <button class="analyze-btn" :disabled="!uploadedImageUrl || isAnalyzing" @click="startAnalyze">
                {{ isAnalyzing ? '分析中...' : '开始分析' }}
              </button>
            </div>
          </section>

          <section class="right-panel">
            <div class="display-panel" ref="displayPanelRef">
              <div v-if="messages.length === 0" class="placeholder">话术内容展示区</div>
              <div v-for="m in messages" :key="m.id" class="msg" :data-role="m.role">
                <div class="role">{{ m.role }}</div>
                <div class="content">{{ m.content }}</div>
              </div>
            </div>
            <div class="chat-box">
              <input
                class="chat-input"
                v-model="chatInput"
                type="text"
                placeholder="和AI对话，输出内容会实时展示到上方"
                @keyup.enter="sendChat"
              />
              <button class="send-btn" @click="sendChat">发送</button>
            </div>
          </section>
        </div>
      </main>
    </div>
  </div>
</template>

<style scoped>
.app-root {
  display: flex;
  flex-direction: column;
  height: 100vh;
}

.app-header {
  height: 56px;
  display: flex;
  align-items: center;
  padding: 0 16px;
  background: #1d4ed8; /* blue-700 */
  color: #ffffff;
  box-shadow: 0 2px 6px rgba(0,0,0,0.08);
}
.brand { font-weight: 700; letter-spacing: 1px; }

.app-body { flex: 1; display: flex; min-height: 0; }

.sidebar {
  width: 200px;
  background: #f3f6ff; /* very light blue */
  color: #1f2937;
  padding: 12px 8px;
  border-right: 1px solid #e5e7eb;
}
.sidebar ul { list-style: none; margin: 0; padding: 0; }
.sidebar li {
  padding: 10px 12px;
  border-radius: 8px;
  cursor: pointer;
}
.sidebar li:hover { background: #e8efff; }
.sidebar li.active { background: #dbe7ff; color: #1d4ed8; }

.content {
  flex: 1;
  display: flex;
  flex-direction: column;
  min-width: 0;
  background: #ffffff;
  color: #111827;
}

.top-tabs {
  display: flex;
  gap: 8px;
  padding: 12px;
  border-bottom: 1px solid #e5e7eb;
}
.tab {
  background: #ffffff;
  color: #1f2937;
  border: 1px solid #e5e7eb;
  padding: 8px 12px;
  border-radius: 999px;
}
.tab:hover { background: #f3f4f6; }
.tab.active { background: #2563eb; border-color: #2563eb; color: #ffffff; }

.main-panels { flex: 1; display: flex; min-height: 0; }
.left-panel { width: 40%; padding: 16px; border-right: 1px solid #e5e7eb; background: #ffffff; }
.right-panel { flex: 1; display: flex; flex-direction: column; padding: 16px; min-width: 0; background: #ffffff; }

.uploader { display: flex; flex-direction: column; gap: 12px; }
.upload-box {
  display: grid;
  place-items: center;
  border: 1px dashed #cbd5e1;
  border-radius: 12px;
  height: 320px;
  color: #6b7280;
  position: relative;
  overflow: hidden;
}
.upload-box input { position: absolute; inset: 0; opacity: 0; cursor: pointer; }
.upload-box img { width: 100%; height: 100%; object-fit: contain; background: #ffffff; }
.analyze-btn { align-self: flex-start; padding: 8px 14px; border-radius: 8px; background: #22c55e; border: none; color: #0b1324; }
.analyze-btn:disabled { opacity: 0.6; cursor: not-allowed; }

.display-panel {
  flex: 1;
  background: #ffffff;
  border: 1px solid #e5e7eb;
  border-radius: 12px;
  padding: 12px;
  overflow: auto;
}
.placeholder { color: #9ca3af; text-align: center; margin-top: 12px; }
.msg { display: grid; grid-template-columns: 64px 1fr; gap: 8px; padding: 8px; }
.msg .role { color: #1d4ed8; font-weight: 600; }
.msg[data-role='我'] .role { color: #16a34a; }
.msg .content { white-space: pre-wrap; color: #111827; }

.chat-box { display: flex; gap: 8px; margin-top: 12px; }
.chat-input { flex: 1; padding: 10px 12px; border-radius: 10px; border: 1px solid #e5e7eb; background: #ffffff; color: #111827; }
.send-btn { padding: 10px 14px; border-radius: 10px; border: 1px solid #2563eb; background: #2563eb; color: #ffffff; }
</style>
