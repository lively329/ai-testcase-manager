<script setup>
import { ref, provide, computed, watch, nextTick } from 'vue';
import { useRouter, useRoute } from 'vue-router';
import { 
  DataAnalysis, 
  Files, 
  Upload, 
  RefreshRight, 
  Brush, 
  Check,
  MagicStick,
  DocumentCopy,
  Operation,
  Switch
} from '@element-plus/icons-vue';
import UploadCase from './views/UploadCase.vue';
import ManageCase from './views/ManageCase.vue';
import AiGenerateCase from './views/AiGenerateCase.vue';
import LogsView from './views/LogsView.vue';
import AiConfig from './views/AiConfig.vue';
import DataFactory from './views/DataFactory.vue';

const router = useRouter();
const route = useRoute();

const sidebarCollapsed = ref(false);
const sidebarDisabled = ref(false);

// 数据工厂子导航状态
const dataFactoryTab = ref('generator');
const dataFactorySubMenuExpanded = ref(true); // 新增：控制数据工厂子导航的展开/收缩

// 创建响应式的项目删除事件
const projectDeletedEvent = ref(null);

// 提供事件给子组件
provide('projectDeletedEvent', projectDeletedEvent);

// 根据当前路由计算当前tab
const tab = computed(() => {
  const path = route.path;
  if (path === '/upload') {
    return 'upload';
  }
  if (path === '/ai-generate') {
    return 'ai-generate';
  }
  if (path === '/logs') {
    return 'logs';
  }
  if (path === '/ai-config') {
    return 'ai-config';
  }
  if (path === '/data-factory') {
    return 'data-factory';
  }
  return 'manage'; // 默认为manage，包括 /manage 和 /
});

// 处理项目删除事件
const handleProjectDeleted = (projectId) => {
  projectDeletedEvent.value = projectId;
};

// 切换侧边栏状态
const toggleSidebar = () => {
  if (!sidebarDisabled.value) {
    sidebarCollapsed.value = !sidebarCollapsed.value;
  }
};

// 处理侧边栏禁用状态
const handleSidebarDisabled = (disabled) => {
  sidebarDisabled.value = disabled;
};

// 处理tab选择
const handleTabSelect = (selectedTab) => {
  if (!sidebarDisabled.value) {
    if (selectedTab === 'upload') {
      router.push('/upload');
    } else if (selectedTab === 'ai-generate') {
      router.push('/ai-generate');
    } else if (selectedTab === 'logs') {
      router.push('/logs');
    } else if (selectedTab === 'ai-config') {
      router.push('/ai-config');
    } else if (selectedTab === 'data-factory') {
      router.push('/data-factory');
    } else {
      router.push('/manage');
    }
  }
};

// 切换数据工厂子导航
const switchDataFactoryTab = (tabName) => {
  dataFactoryTab.value = tabName;
};

// 切换数据工厂子导航的展开/收缩状态
const toggleDataFactorySubMenu = () => {
  dataFactorySubMenuExpanded.value = !dataFactorySubMenuExpanded.value;
};

// 获取主导航面包屑文本
const getMainTabText = () => {
  const titles = {
    'upload': '用例上传',
    'ai-generate': 'AI生成用例',
    'logs': '运行日志',
    'ai-config': '系统设置',
    'data-factory': '数据工厂',
    'manage': '项目用例管理'
  };
  return titles[tab.value] || '项目用例管理';
};

// 获取子导航面包屑文本
const getSubTabText = () => {
  const subTabTitles = {
    'generator': '数据生成器',
    'template': '数据模板',
    'batch': '批量生成',
    'transform': '格式转换'
  };
  return subTabTitles[dataFactoryTab.value] || '数据工厂';
};

// 监听路由变化，自动恢复侧边栏状态
watch(() => route.path, (newPath) => {
  // 当路由变化时，自动恢复侧边栏状态
  sidebarDisabled.value = false;
  
  // 如果切换到数据工厂，确保子导航展开
  if (newPath === '/data-factory') {
    dataFactorySubMenuExpanded.value = true;
  } else {
    // 如果切换到其他页面，数据工厂子导航收起
    dataFactorySubMenuExpanded.value = false;
  }
});
</script>

<template>
  <el-container style="height: 100vh; width: 100vw; margin: 0; padding: 0;">
    <!-- 美化后的侧边栏 -->
    <el-aside :width="sidebarCollapsed ? '80px' : '240px'" class="sidebar" :class="{ 'collapsed': sidebarCollapsed, 'disabled': sidebarDisabled }">
      <!-- Logo区域 -->
      <div class="sidebar-header">
        <div class="logo-container" :class="{ 'collapsed': sidebarCollapsed }">
          <div class="logo-icon">
            <img src="/src/icon/橘猫.png" alt="Logo" />
          </div>
          <div class="logo-text" v-show="!sidebarCollapsed">
            <h1>AIsystem</h1>
            <p>Case Manager</p>
          </div>
        </div>
      </div>

      <!-- 导航菜单 -->
      <div class="sidebar-menu">
        <el-menu 
          :default-active="tab" 
          class="custom-menu" 
          background-color="transparent" 
          text-color="#E2E8F0" 
          active-text-color="#60A5FA"
          @select="handleTabSelect"
          :disabled="sidebarDisabled"
        >
          <el-menu-item index="manage" class="menu-item">
            <div class="menu-item-content">
              <div class="menu-icon">
                <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <path d="M19 11H5M19 11C20.1046 11 21 11.8954 21 13V19C21 20.1046 20.1046 21 19 21H5C3.89543 21 3 20.1046 3 19V13C3 11.8954 3.89543 11 5 11M19 11V9C19 7.89543 18.1046 7 17 7M5 11V9C5 7.89543 5.89543 7 7 7M7 7V5C7 3.89543 7.89543 3 9 3H15C16.1046 3 17 3.89543 17 5V7M7 7H17" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                </svg>
              </div>
              <span v-show="!sidebarCollapsed">项目用例管理</span>
            </div>
          </el-menu-item>
          
          <el-menu-item index="upload" class="menu-item">
            <div class="menu-item-content">
              <div class="menu-icon">
                <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <path d="M21 15V19C21 19.5304 20.7893 20.0391 20.4142 20.4142C20.0391 20.7893 19.5304 21 19 21H5C4.46957 21 3.96086 20.7893 3.58579 20.4142C3.21071 20.0391 3 19.5304 3 19V15M17 8L12 3M12 3L7 8M12 3V15" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                </svg>
              </div>
              <span v-show="!sidebarCollapsed">用例上传</span>
            </div>
          </el-menu-item>

          <el-menu-item index="ai-generate" class="menu-item">
            <div class="menu-item-content">
              <div class="menu-icon">
                <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <rect x="4" y="8" width="16" height="10" rx="4" stroke="currentColor" stroke-width="2"/>
                  <rect x="9" y="2" width="6" height="4" rx="2" stroke="currentColor" stroke-width="2"/>
                  <circle cx="8.5" cy="13.5" r="1.5" fill="currentColor"/>
                  <circle cx="15.5" cy="13.5" r="1.5" fill="currentColor"/>
                  <path d="M12 18v2" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
                </svg>
              </div>
              <span v-show="!sidebarCollapsed">AI生成用例</span>
            </div>
          </el-menu-item>

          <el-menu-item index="logs" class="menu-item">
            <div class="menu-item-content">
              <div class="menu-icon">
                <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <path d="M14 2H6C5.46957 2 4.96086 2.21071 4.58579 2.58579C4.21071 2.96086 4 3.46957 4 4V20C4 20.5304 4.21071 21.0391 4.58579 21.4142C4.96086 21.7893 5.46957 22 6 22H18C18.5304 22 19.0391 21.7893 19.4142 21.4142C19.7893 21.5304 20 20.5304 20 20V8L14 2Z" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                  <path d="M14 2V8H20" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                  <path d="M16 13H8" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                  <path d="M16 17H8" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                  <path d="M10 9H9H8" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                </svg>
              </div>
              <span v-show="!sidebarCollapsed">运行日志</span>
            </div>
          </el-menu-item>

          <el-menu-item index="ai-config" class="menu-item">
            <div class="menu-item-content">
              <div class="menu-icon">
                <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <path d="M12 2L2 7L12 12L22 7L12 2Z" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                  <path d="M2 17L12 22L22 17" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                  <path d="M2 12L12 17L22 12" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                </svg>
              </div>
              <span v-show="!sidebarCollapsed">系统设置</span>
            </div>
          </el-menu-item>

          <el-menu-item index="data-factory" class="menu-item" @click="toggleDataFactorySubMenu">
            <div class="menu-item-content">
              <div class="menu-icon">
                <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <path d="M3 3H21V21H3V3Z" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                  <path d="M9 9H15V15H9V9Z" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                  <path d="M3 9H21" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                  <path d="M9 3V21" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                  <path d="M15 3V21" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                </svg>
              </div>
              <span v-show="!sidebarCollapsed">数据工厂</span>
              <div v-show="!sidebarCollapsed" class="expand-arrow" :class="{ 'expanded': dataFactorySubMenuExpanded }">
                <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <path d="M6 9L12 15L18 9" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                </svg>
              </div>
            </div>
          </el-menu-item>
          
          <!-- 数据工厂子导航 -->
          <div v-if="tab === 'data-factory' && dataFactorySubMenuExpanded" 
               class="sub-menu-container" 
               :class="{ 'expanded': dataFactorySubMenuExpanded }">
            <div class="sub-menu">
              <div class="sub-menu-item" @click="switchDataFactoryTab('generator')" :class="{ active: dataFactoryTab === 'generator' }">
                <el-icon><MagicStick /></el-icon>
                <span v-show="!sidebarCollapsed">数据生成器</span>
              </div>
              <div class="sub-menu-item" @click="switchDataFactoryTab('template')" :class="{ active: dataFactoryTab === 'template' }">
                <el-icon><DocumentCopy /></el-icon>
                <span v-show="!sidebarCollapsed">数据模板</span>
              </div>
              <div class="sub-menu-item" @click="switchDataFactoryTab('batch')" :class="{ active: dataFactoryTab === 'batch' }">
                <el-icon><Operation /></el-icon>
                <span v-show="!sidebarCollapsed">批量生成</span>
              </div>
              <div class="sub-menu-item" @click="switchDataFactoryTab('transform')" :class="{ active: dataFactoryTab === 'transform' }">
                <el-icon><Switch /></el-icon>
                <span v-show="!sidebarCollapsed">格式转换</span>
              </div>

            </div>
          </div>
      </el-menu>
      </div>

      <!-- 底部信息 -->
      <div class="sidebar-footer">
        <div class="footer-info">
          <!-- 可以在这里添加其他信息 -->
        </div>
      </div>
      
      <!-- 收缩按钮 -->
      <div class="sidebar-toggle" @click="toggleSidebar">
        <div class="toggle-button">
          <svg v-if="!sidebarCollapsed" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M15 18L9 12L15 6" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
          </svg>
          <svg v-else viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M9 18L15 12L9 6" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
          </svg>
        </div>
      </div>
    </el-aside>

          <!-- 主内容区域 -->
    <el-container>
        <el-header height="80px" class="main-header">
          <div class="header-content">
            <div class="breadcrumb-container">
              <div class="breadcrumb">
                <span class="breadcrumb-item">首页</span>
                <span class="breadcrumb-separator">/</span>
                <transition name="fade" mode="out-in">
                  <div :key="`${tab}-${dataFactoryTab}`" class="breadcrumb-main-section">
                    <span class="breadcrumb-item active">
                      {{ getMainTabText() }}
                    </span>
                    <span v-if="tab === 'data-factory'" class="breadcrumb-separator">/</span>
                    <span v-if="tab === 'data-factory'" class="breadcrumb-item active" data-sub-tab>
                      {{ getSubTabText() }}
                    </span>
                  </div>
                </transition>
              </div>
            </div>
            <!-- <div class="header-actions">
              <a href="" class="email-link" title="">
                <svg viewBox="0 0 24 24" fill="currentColor" class="email-icon">
                  <path d="M2.003 5.884L10 9.882l7.997-3.998A2 2 0 0016 4H4a2 2 0 00-1.997 1.884z"/>
                  <path d="M18 8.118l-8 4-8-4V14a2 2 0 002 2h12a2 2 0 002-2V8.118z"/>
                </svg>
              </a>
              <a href="" target="_blank" class="github-link">
                <svg viewBox="0 0 24 24" fill="currentColor" class="github-icon">
                  <path d="M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z"/>
                </svg>
              </a>
            </div> -->
          </div>
      </el-header>
      
      <el-main class="main-content">
        <UploadCase v-if="tab==='upload'" />
        <AiGenerateCase v-else-if="tab==='ai-generate'" />
        <LogsView v-else-if="tab==='logs'" />
        <AiConfig v-else-if="tab==='ai-config'" />
        <DataFactory v-else-if="tab==='data-factory'" :current-tab="dataFactoryTab" />
        <ManageCase v-else @project-deleted="handleProjectDeleted" @sidebar-disabled="handleSidebarDisabled" />
      </el-main>
    </el-container>
  </el-container>
</template>

<style scoped>
/* 侧边栏样式 */
.sidebar {
  background: linear-gradient(180deg, #0eb2e9 , #f8c538 50%, #e3fc7d 100%);
  box-shadow: 4px 0 20px rgba(14, 165, 233, 0.2);
  position: relative;
  overflow: visible;
  z-index: 10;
  border-top-right-radius: 12px;
  border-bottom-right-radius: 12px;
}

.sidebar::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.08) 0%, rgba(255, 255, 255, 0.03) 100%);
  pointer-events: none;
}

/* Logo区域 */
.sidebar-header {
  padding: 32px 24px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.logo-container {
  display: flex;
  align-items: center;
  gap: 16px;
  width: 100%;
}

.logo-icon {
  width: 56px;
  height: 56px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
}

.logo-icon svg {
  width: 24px;
  height: 24px;
}

.logo-icon img {
  width: 40px;
  height: 40px;
  border-radius: 10px;
  object-fit: cover;
}

.logo-text h1 {
  margin: 0;
  font-size: 22px;
  font-weight: 700;
  color: white;
  line-height: 1.2;
  transition: opacity 0.3s ease;
  font-family: 'PingFang SC', 'Helvetica Neue', 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', Arial, sans-serif !important;
}

.logo-text p {
  margin: 4px 0 0 0;
  font-size: 14px;
  color: rgba(255, 255, 255, 0.8);
  font-weight: 400;
  transition: opacity 0.3s ease;
  font-family: 'PingFang SC', 'Helvetica Neue', 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', Arial, sans-serif !important;
}

/* 菜单区域 */
.sidebar-menu {
  padding: 24px 0;
  flex: 1;
  overflow-y: auto;
  max-height: calc(100vh - 200px);
  scrollbar-width: none; /* Firefox */
  -ms-overflow-style: none; /* IE and Edge */
}

.custom-menu {
  border: none;
  background: transparent;
}

.menu-item {
  margin: 8px 16px;
  border-radius: 12px;
  height: 56px;
  transition: all 0.3s ease;
}

.menu-item:hover {
  background: rgba(255, 255, 255, 0.8) !important;
  transform: translateX(4px);
}

.menu-item.is-active {
  background: rgba(255, 255, 255, 0.95) !important;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.8);
}

.menu-item-content {
  display: flex;
  align-items: center;
  gap: 16px;
  height: 100%;
  width: 100%;
}

.menu-item-content span {
  transition: opacity 0.3s ease;
  font-weight: 600 !important;
  font-size: 15px !important;
  font-family: 'PingFang SC', 'Helvetica Neue', 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', Arial, sans-serif !important;
  flex: 1; /* 让文字占据剩余空间 */
}

.expand-arrow {
  width: 16px;
  height: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  color: rgba(255, 255, 255, 0.8); /* 默认白色半透明 */
}

/* 主导航选中状态下的箭头颜色 */
.menu-item.is-active .expand-arrow {
  color: #1f2937; /* 选中状态下使用深色 */
}

.expand-arrow.expanded {
  transform: rotate(180deg);
}

.expand-arrow svg {
  width: 16px;
  height: 16px;
}

.menu-icon {
  width: 20px;
  height: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.menu-icon svg {
  width: 18px;
  height: 18px;
}

/* 底部信息 */
.sidebar-footer {
  padding: 24px;
  border-top: 1px solid rgba(255, 255, 255, 0.1);
}

.footer-info {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

/* 收缩按钮 */
.sidebar-toggle {
  position: absolute;
  top: 50%;
  right: -16px;
  transform: translateY(-50%);
  z-index: 9999;
}

.toggle-button {
  width: 32px;
  height: 32px;
  background: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #6B7280;
  cursor: pointer;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
  transition: all 0.3s ease;
  border: 2px solid #E5E7EB;
}

.toggle-button:hover {
  background: #F3F4F6;
  color: #374151;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
  transform: scale(1.05);
}

.toggle-button svg {
  width: 16px;
  height: 16px;
  transition: transform 0.3s ease;
}

/* 侧边栏动画 */
.sidebar {
  transition: width 0.3s ease;
}

/* 收缩状态样式 */
.sidebar.collapsed .logo-container {
  justify-content: center;
  align-items: center;
  padding: 0;
  width: 100%;
}

.sidebar.collapsed .logo-icon {
  margin: 0;
  width: 44px;
  height: 44px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.sidebar.collapsed .logo-icon svg {
  width: 18px;
  height: 18px;
}

.sidebar.collapsed .logo-icon img {
  width: 32px;
  height: 32px;
  border-radius: 8px;
  object-fit: cover;
}

/* 主导航收缩状态样式 */
.sidebar.collapsed .menu-item {
  height: 56px; /* 保持主导航的重要性，比子导航大 */
  width: 56px; /* 固定宽度，形成正方形 */
  margin: 4px auto; /* 居中显示 */
  border-radius: 12px; /* 保持主导航的圆角 */
}

.sidebar.collapsed .menu-item-content {
  justify-content: center;
  align-items: center;
  padding: 0;
  width: 100%;
}

.sidebar.collapsed .menu-icon {
  margin: 0;
  width: 20px;
  height: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.sidebar.collapsed .menu-icon svg {
  width: 16px;
  height: 16px;
}

.sidebar.collapsed .sidebar-header {
  padding: 24px 0;
}

.sidebar.collapsed .sidebar-menu {
  padding: 16px 0;
}

.sidebar.collapsed .sidebar-footer {
  padding: 16px 0;
}

/* 主内容区域 */
.main-header {
  background: linear-gradient(135deg, #ffffff 0%, #f8fafc 100%);
  border-bottom: 1px solid #e2e8f0;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.05);
  padding: 0 40px;
  position: relative;
  z-index: 1;
}

.header-content {
  display: flex;
  align-items: center;
  justify-content: space-between;
  height: 100%;
  padding-left: 0;
}

/* 面包屑导航区域 */
.breadcrumb-container {
  display: flex;
  align-items: center;
  margin-left: -40px;
  overflow: hidden;
}

.breadcrumb {
  display: flex;
  align-items: center;
  gap: 4px;
  padding: 12px 20px;
  background: rgba(255, 255, 255, 0.8);
  border-radius: 12px;
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.3);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  position: relative;
  min-height: 24px;
  min-width: 300px;
}



.breadcrumb-item {
  font-size: 16px;
  color: #6B7280;
  font-weight: 500;
  transition: color 0.3s ease;
  font-family: 'PingFang SC', 'Helvetica Neue', 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', Arial, sans-serif;
  display: flex;
  align-items: center;
  height: 1.2em;
  line-height: 1.2;
  white-space: nowrap;
  margin: 0;
  padding: 0;
}

.breadcrumb-item.active {
  color: #1F2937;
  font-weight: 600;
  position: relative;
  display: flex;
  align-items: center;
  height: 1.2em;
  line-height: 1.2;
  white-space: nowrap;
}

/* 为子导航面包屑项添加特殊样式 */
.breadcrumb-item.active[data-sub-tab] {
  white-space: nowrap;
}

.breadcrumb-separator {
  color: #9CA3AF;
  font-size: 14px;
  font-weight: 400;
  display: flex;
  align-items: center;
  height: 1.2em;
  line-height: 1.2;
  margin: 0;
  padding: 0;
}

/* 主导航部分容器样式 */
.breadcrumb-main-section {
  display: flex;
  align-items: center;
  gap: 4px;
}

/* 淡入淡出动画 */
.fade-enter-active,
.fade-leave-active {
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
  display: flex;
  align-items: center;
  height: 1.2em;
  line-height: 1.2;
}

.fade-enter-from {
  opacity: 0;
  transform: translateY(-15px) scale(0.95);
}

.fade-leave-to {
  opacity: 0;
  transform: translateY(15px) scale(0.95);
}

.fade-enter-to,
.fade-leave-from {
  opacity: 1;
  transform: translateY(0) scale(1);
}

/* GitHub图标样式 */
.header-actions {
  display: flex;
  align-items: center;
  gap: 16px;
}

.email-link {
  display: flex;
  align-items: center;
  justify-content: center;
  color: #333;
  text-decoration: none;
  padding: 0;
  transform: translateY(4px);
}

.email-link:hover {
  background-color: transparent !important;
}

.email-icon {
  width: 26px;
  height: 26px;
  transition: all 0.3s ease;
}

.github-link {
  display: flex;
  align-items: center;
  justify-content: center;
  color: #333;
  text-decoration: none;
  padding: 0;
}

.github-link:hover {
  background-color: transparent !important;
}

.github-icon {
  width: 22px;
  height: 22px;
  transition: all 0.3s ease;
}

.main-content {
  background: #e2e8f0;
  padding: 32px 40px;
  height: calc(100vh - 70px);
  overflow: auto;
  position: relative;
  z-index: 1;
}

/* 全局样式 */
:deep(.el-menu-item) {
  color: #e0f2fe !important;
  font-weight: 700 !important;
  font-family: 'PingFang SC', 'Helvetica Neue', 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', Arial, sans-serif !important;
}

:deep(.el-menu-item.is-active) {
  color: #1f2937 !important;
  font-weight: 800 !important;
  font-family: 'PingFang SC', 'Helvetica Neue', 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', Arial, sans-serif !important;
}

:deep(.el-menu-item:hover) {
  color: #1f2937 !important;
  font-weight: 700 !important;
  font-family: 'PingFang SC', 'Helvetica Neue', 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', Arial, sans-serif !important;
}

/* 禁用状态样式 */
.sidebar.disabled {
  position: relative;
}

.sidebar.disabled::after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.3);
  z-index: 100;
  pointer-events: none;
}

:deep(.el-menu.is-disabled) {
  opacity: 0.6;
  pointer-events: none;
}

:deep(.el-menu.is-disabled .el-menu-item) {
  color: #9CA3AF !important;
  background: transparent !important;
}

:deep(.el-menu.is-disabled .el-menu-item:hover) {
  color: #9CA3AF !important;
  background: transparent !important;
}

:deep(.el-menu.is-disabled .el-menu-item.is-active) {
  color: #9CA3AF !important;
  background: transparent !important;
}

/* 禁用状态下的收缩按钮 */
.sidebar.disabled .sidebar-toggle {
  opacity: 0.6;
  pointer-events: none;
}

.sidebar.disabled .toggle-button {
  opacity: 0.3;
  pointer-events: none;
  background: rgba(0, 0, 0, 0.3);
  color: rgba(255, 255, 255, 0.5);
  border-color: rgba(255, 255, 255, 0.2);
}

.sidebar.disabled .toggle-button:hover {
  background: rgba(0, 0, 0, 0.3);
  color: rgba(255, 255, 255, 0.5);
  transform: none;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

/* 禁用状态下的菜单项 */
.sidebar.disabled .menu-item {
  pointer-events: none;
}

.sidebar.disabled .menu-item:hover {
  background: transparent !important;
  transform: none !important;
}

.sidebar.disabled .menu-item.is-active {
  background: transparent !important;
}

/* 数据工厂子导航样式 */
.sub-menu-container {
  margin-top: 8px;
  margin-bottom: 16px;
  overflow: hidden; /* 隐藏超出部分 */
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1); /* 增加动画时间，更平滑 */
  max-height: 0; /* 初始收起状态 */
  opacity: 0; /* 初始透明 */
}

.sub-menu-container.expanded {
  max-height: 300px; /* 展开状态的最大高度 */
  opacity: 1; /* 展开状态完全显示 */
}

.sub-menu {
  display: flex;
  flex-direction: column;
  gap: 4px;
  padding: 0 20px 0 40px; /* 左侧增加缩进 */
  transform-origin: top; /* 设置变换原点为顶部 */
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1); /* 平滑过渡动画 */
}

/* 子导航展开/收起动画 */
.sub-menu-container-enter-active,
.sub-menu-container-leave-active {
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.sub-menu-container-enter-from {
  opacity: 0;
  max-height: 0;
  transform: translateY(-10px);
}

.sub-menu-container-enter-to {
  opacity: 1;
  max-height: 300px; /* 设置最大高度 */
  transform: translateY(0);
}

.sub-menu-container-leave-from {
  opacity: 1;
  max-height: 300px;
  transform: translateY(0);
}

.sub-menu-container-leave-to {
  opacity: 0;
  max-height: 0;
  transform: translateY(-10px);
}

.sub-menu-item {
  display: flex;
  align-items: center;
  padding: 8px 16px;
  cursor: pointer;
  transition: all 0.3s ease;
  color: rgba(255, 255, 255, 0.8);
  border-radius: 12px; /* 与主导航保持一致 */
  font-size: 14px;
  gap: 12px;
  position: relative;
  height: 48px; /* 稍微小一点，但保持比例 */
  margin: 4px 0; /* 添加垂直间距 */
  opacity: 0; /* 初始透明 */
  transform: translateX(-20px); /* 初始向左偏移 */
  animation: slideInItem 0.3s ease forwards; /* 进入动画 */
  white-space: nowrap; /* 防止文字换行 */
  writing-mode: horizontal-tb; /* 确保文字水平显示 */
}

/* 子导航项进入动画 */
@keyframes slideInItem {
  0% {
    opacity: 0;
    transform: translateX(-20px);
  }
  100% {
    opacity: 1;
    transform: translateX(0);
  }
}

/* 为每个子导航项设置不同的动画延迟 */
.sub-menu-item:nth-child(1) { animation-delay: 0.1s; }
.sub-menu-item:nth-child(2) { animation-delay: 0.15s; }
.sub-menu-item:nth-child(3) { animation-delay: 0.2s; }
.sub-menu-item:nth-child(4) { animation-delay: 0.25s; }

.sub-menu-item::before {
  content: '';
  position: absolute;
  left: -16px;
  top: 50%;
  width: 12px;
  height: 1px;
  background: rgba(255, 255, 255, 0.3);
  transform: translateY(-50%);
}

.sub-menu-item:hover {
  background: rgba(255, 255, 255, 0.8) !important; /* 与主导航hover保持一致 */
  color: #1f2937 !important; /* 与主导航hover保持一致 */
  transform: translateX(4px); /* 与主导航hover保持一致 */
}

.sub-menu-item.active {
  background: rgba(255, 255, 255, 0.95) !important; /* 与主导航active保持一致 */
  color: #1f2937 !important; /* 与主导航active保持一致 */
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1); /* 与主导航active保持一致 */
  border: 1px solid rgba(255, 255, 255, 0.8); /* 与主导航active保持一致 */
  font-weight: 600; /* 与主导航active保持一致 */
}

.sub-menu-item.active::before {
  background: rgba(255, 255, 255, 0.8); /* 激活状态下的连接线更亮 */
}

.sub-menu-item .el-icon {
  font-size: 16px;
  flex-shrink: 0;
}

/* 侧边栏收缩时的子导航样式 */
.sidebar.collapsed .sub-menu {
  padding: 0 8px 0 16px; /* 收缩时减少缩进 */
}

.sidebar.collapsed .sub-menu-item {
  padding: 8px;
  justify-content: center;
  height: 40px; /* 与主导航收缩时的高度保持一致 */
  width: 40px; /* 固定宽度，与主导航保持一致 */
  margin: 4px auto; /* 居中显示 */
  border-radius: 8px; /* 收缩时使用较小的圆角 */
}

.sidebar.collapsed .sub-menu-item span {
  display: none;
}

.sidebar.collapsed .sub-menu-item::before {
  display: none; /* 收缩时隐藏连接线 */
}

.sidebar.collapsed .sub-menu-item .el-icon {
  font-size: 16px; /* 与主导航收缩时的图标大小保持一致 */
  width: 20px; /* 与主导航图标容器大小保持一致 */
  height: 20px; /* 与主导航图标容器大小保持一致 */
  display: flex;
  align-items: center;
  justify-content: center;
}

.sub-menu-item span {
  white-space: nowrap; /* 防止文字换行 */
  writing-mode: horizontal-tb; /* 确保文字水平显示 */
  text-orientation: mixed; /* 确保文字方向正确 */
  display: inline-block; /* 确保文字块级显示 */
}
</style>

<style>
html, body, #app {
  height: 100%;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
}

/* 滚动条样式 */
::-webkit-scrollbar {
  width: 6px;
}

::-webkit-scrollbar-track {
  background: #F1F5F9;
}

::-webkit-scrollbar-thumb {
  background: #CBD5E1;
  border-radius: 3px;
}

::-webkit-scrollbar-thumb:hover {
  background: #94A3B8;
}

/* 侧边栏菜单滚动条样式 */
.sidebar-menu::-webkit-scrollbar {
  display: none; /* Chrome, Safari and Opera */
}
</style>