<template>
  <div class="admin-layout">
    <el-container class="layout-container">
      
      <el-aside width="260px" class="sidebar">
        <div class="brand-area">
          <div class="brand-icon">
            <el-icon><TrendCharts /></el-icon>
          </div>
          <h2 class="brand-text">Sentimen<span class="text-primary">Pro</span></h2>
        </div>

        <el-menu
          :default-active="activeMenu"
          class="custom-menu"
          router
        >
          <div class="menu-label">MENU UTAMA</div>
          <el-menu-item index="/admin/dashboard">
            <el-icon><Odometer /></el-icon>
            <span>Dashboard</span>
          </el-menu-item>
          <el-menu-item index="/admin/upload">
            <el-icon><UploadFilled /></el-icon>
            <span>Upload Data</span>
          </el-menu-item>
          <el-menu-item index="/admin/results">
            <el-icon><DataLine /></el-icon>
            <span>Hasil Analisis</span>
          </el-menu-item>

        </el-menu>

        <div class="sidebar-footer">
          <div class="user-info">
            <el-avatar :size="32" icon="UserFilled" />
            <div class="user-details">
              <span class="name">Administrator</span>
              <span class="role">Super Admin</span>
            </div>
          </div>
        </div>
      </el-aside>

      <el-container>
        <el-header class="main-header">
          <div class="header-left">
            <h3>Dashboard</h3>
            <span class="date-now">{{ currentDate }}</span>
          </div>
          <div class="header-right">
            <el-button type="danger" plain size="small" @click="handleLogout">
              <el-icon class="mr-1"><SwitchButton /></el-icon> Logout
            </el-button>
          </div>
        </el-header>

        <el-main class="main-content">
          <div class="hero-section">
            <div class="hero-content">
              <el-tag effect="dark" round class="mb-2">Versi 2.0</el-tag>
              <h1 class="hero-title">Selamat Datang di SentimenPro</h1>
              <p class="hero-desc">
                Platform analisis sentimen berbasis kecerdasan buatan (AI) yang dirancang untuk membantu Anda memahami umpan balik pelanggan secara cepat, akurat, dan efisien.
              </p>
              <div class="hero-actions">
                <el-button type="primary" size="large" @click="$router.push('/admin/upload')">
                  Mulai Analisis Baru <el-icon class="el-icon--right"><Right /></el-icon>
                </el-button>
              </div>
            </div>
            <div class="hero-illustration">
              <div class="circle-decor c1"></div>
              <div class="circle-decor c2"></div>
              <el-icon class="hero-icon"><TrendCharts /></el-icon>
            </div>
          </div>

          <div class="stats-overview" v-if="statsData">
            <el-card shadow="hover" class="stats-card">
              <template #header>
                <div class="stats-header">
                  <div class="header-title">
                    <el-icon class="header-icon"><PieChart /></el-icon>
                    <h3>Ringkasan Analisis Terakhir</h3>
                  </div>
                  <div class="header-meta">
                     <el-tag size="small" type="info" effect="plain" class="date-tag">
                        {{ formatDate(statsData.latest_analysis.date) }}
                     </el-tag>
                     <el-button type="primary" link @click="$router.push('/admin/results')">
                        Lihat Detail <el-icon class="el-icon--right"><ArrowRight /></el-icon>
                     </el-button>
                  </div>
                </div>
              </template>
              
              <div class="stats-content">
                <!-- Pie Chart Area -->
                <div class="chart-section">
                  <div class="chart-container" v-if="chartData">
                    <Pie :data="chartData" :options="chartOptions" />
                  </div>
                  <div v-else class="flex items-center justify-center h-full text-gray-400">
                    <p>Menunggu data...</p>
                  </div>
                </div>
                
                <!-- Stats Details Area -->
                <div class="details-section">
                   <div class="total-stat-box">
                      <span class="label">Total Data Dianalisis</span>
                      <span class="value">{{ statsData.latest_analysis.total }}</span>
                   </div>
                   
                   <div class="sentiment-bars">
                      <!-- Positif -->
                      <div class="sentiment-item">
                         <div class="item-header">
                            <span class="label text-success">Positif</span>
                            <span class="count">{{ statsData.latest_analysis.positive }} ({{ getPercentage(statsData.latest_analysis.positive, statsData.latest_analysis.total) }}%)</span>
                         </div>
                         <el-progress 
                           :percentage="getPercentage(statsData.latest_analysis.positive, statsData.latest_analysis.total)" 
                           status="success" 
                           :stroke-width="8"
                           :show-text="false"
                         />
                      </div>

                      <!-- Negatif -->
                      <div class="sentiment-item">
                         <div class="item-header">
                            <span class="label text-danger">Negatif</span>
                            <span class="count">{{ statsData.latest_analysis.negative }} ({{ getPercentage(statsData.latest_analysis.negative, statsData.latest_analysis.total) }}%)</span>
                         </div>
                         <el-progress 
                           :percentage="getPercentage(statsData.latest_analysis.negative, statsData.latest_analysis.total)" 
                           status="exception" 
                           :stroke-width="8"
                           :show-text="false"
                         />
                      </div>

                      <!-- Netral -->
                      <div class="sentiment-item">
                         <div class="item-header">
                            <span class="label text-warning">Netral</span>
                            <span class="count">{{ statsData.latest_analysis.neutral }} ({{ getPercentage(statsData.latest_analysis.neutral, statsData.latest_analysis.total) }}%)</span>
                         </div>
                         <el-progress 
                           :percentage="getPercentage(statsData.latest_analysis.neutral, statsData.latest_analysis.total)" 
                           status="warning" 
                           :stroke-width="8"
                           :show-text="false"
                         />
                      </div>
                   </div>
                </div>
              </div>
            </el-card>
          </div>

          <div class="info-grid">
            <el-card class="info-card" shadow="hover">
              <div class="card-icon bg-indigo">
                <el-icon><UploadFilled /></el-icon>
              </div>
              <h3>1. Upload Data</h3>
              <p>Unggah file data ulasan pelanggan Anda dalam format <b>.CSV</b> atau <b>.XLSX</b>. Sistem kami mendukung pemrosesan ribuan baris data sekaligus (Batch Processing).</p>
            </el-card>

            <el-card class="info-card" shadow="hover">
              <div class="card-icon bg-rose">
                <el-icon><Cpu /></el-icon>
              </div>
              <h3>2. Pemrosesan AI</h3>
              <p>Algoritma Natural Language Processing (NLP) kami akan secara otomatis mengklasifikasikan sentimen menjadi <b>Positif</b>, <b>Negatif</b>, atau <b>Netral</b>.</p>
            </el-card>

            <el-card class="info-card" shadow="hover">
              <div class="card-icon bg-emerald">
                <el-icon><DataAnalysis /></el-icon>
              </div>
              <h3>3. Hasil & Laporan</h3>
              <p>Lihat visualisasi grafik interaktif, filter hasil berdasarkan tanggal, dan unduh laporan lengkap untuk presentasi bisnis Anda.</p>
            </el-card>
          </div>

          <el-card class="system-info" shadow="never">
            <div class="info-row">
              <div class="info-item">
                <span class="label">Status Server</span>
                <span class="value status-ok"><span class="dot"></span> Operasional</span>
              </div>
              <el-divider direction="vertical" />
              <div class="info-item" v-if="statsData && statsData.system_stats">
                <span class="label">Total User</span>
                <span class="value">{{ statsData.system_stats.total_users }} Pengguna</span>
              </div>
              <el-divider direction="vertical" v-if="statsData && statsData.system_stats" />
              <div class="info-item">
                <span class="label">Update Terakhir</span>
                <span class="value">16 Januari 2026</span>
              </div>
              <el-divider direction="vertical" />
              <div class="info-item">
                <span class="label">Lisensi</span>
                <span class="value">Enterprise Edition</span>
              </div>
            </div>
          </el-card>

        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script setup>
import { computed, ref, onMounted } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import { ElMessage } from 'element-plus'
import { 
  TrendCharts, Odometer, UploadFilled, DataLine, 
  User, SwitchButton, Right, Cpu, DataAnalysis,
  PieChart, Document, ArrowRight
} from '@element-plus/icons-vue'
import { useAuthStore } from '@/stores/auth'
import api from '@/services/api'
import { Pie } from 'vue-chartjs'
import { Chart as ChartJS, ArcElement, Tooltip, Legend } from 'chart.js'

ChartJS.register(ArcElement, Tooltip, Legend)

const router = useRouter()
const route = useRoute()
const authStore = useAuthStore()

// State
const statsData = ref(null)
const chartData = ref(null)
const chartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      position: 'right'
    }
  }
}

const activeMenu = computed(() => route.path)
const currentDate = new Date().toLocaleDateString('id-ID', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' })

const formatDate = (dateString) => {
  return new Date(dateString).toLocaleDateString('id-ID', {
    day: 'numeric', month: 'short', year: 'numeric', hour: '2-digit', minute: '2-digit'
  })
}

const getPercentage = (value, total) => {
  if (!total || total === 0) return 0
  return Math.round((value / total) * 100)
}

const fetchStats = async () => {
  try {
    const res = await api.get('/admin/dashboard/stats')
    // Ensure statsData is set even if latest_analysis is null, to avoid v-if hiding everything
    statsData.value = res.data
    
    if (res.data.latest_analysis) {
      chartData.value = {
        labels: ['Positif', 'Negatif', 'Netral'],
        datasets: [
          {
            backgroundColor: ['#10b981', '#ef4444', '#f59e0b'],
            data: [
              res.data.latest_analysis.positive,
              res.data.latest_analysis.negative,
              res.data.latest_analysis.neutral
            ]
          }
        ]
      }
    }
  } catch (error) {
    console.error('Failed to fetch admin stats', error)
  }
}

onMounted(() => {
  fetchStats()
})

const handleLogout = () => {
  authStore.logout()
  ElMessage.success('Logout berhasil')
  router.push('/login')
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');

.admin-layout {
  min-height: 100vh;
  background-color: #f8fafc;
  font-family: 'Inter', sans-serif;
}

.layout-container {
  min-height: 100vh;
}

/* --- Sidebar (Konsisten) --- */
.sidebar {
  background: white;
  border-right: 1px solid #e2e8f0;
  display: flex;
  flex-direction: column;
}

.brand-area {
  height: 70px;
  display: flex;
  align-items: center;
  padding: 0 24px;
  gap: 12px;
  border-bottom: 1px solid #f1f5f9;
}

.brand-icon {
  width: 32px;
  height: 32px;
  background: linear-gradient(135deg, #4f46e5 0%, #6366f1 100%);
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
}

.brand-text {
  font-size: 18px;
  font-weight: 700;
  color: #0f172a;
  margin: 0;
}
.text-primary { color: #4f46e5; }

.custom-menu {
  border-right: none;
  padding-top: 20px;
  flex: 1;
}

.menu-label {
  padding: 0 24px;
  margin-bottom: 8px;
  font-size: 11px;
  font-weight: 700;
  color: #94a3b8;
  letter-spacing: 0.05em;
}

:deep(.el-menu-item) {
  margin: 4px 12px;
  border-radius: 8px;
  height: 44px;
  line-height: 44px;
  color: #64748b;
}

:deep(.el-menu-item.is-active) {
  background-color: #eef2ff;
  color: #4f46e5;
  font-weight: 600;
}

:deep(.el-menu-item:hover) {
  background-color: #f8fafc;
}

.sidebar-footer {
  padding: 20px;
  border-top: 1px solid #f1f5f9;
}

.user-info {
  display: flex;
  align-items: center;
  gap: 12px;
}

.user-details {
  display: flex;
  flex-direction: column;
}

.user-details .name { font-size: 14px; font-weight: 600; color: #0f172a; }
.user-details .role { font-size: 12px; color: #64748b; }

/* --- Main Header --- */
.main-header {
  height: 70px;
  background: white;
  border-bottom: 1px solid #e2e8f0;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 32px;
}

.header-left h3 { margin: 0; font-size: 20px; color: #0f172a; }
.date-now { font-size: 13px; color: #64748b; }

/* --- Main Content --- */
.main-content {
  padding: 32px;
}

/* Hero Section */
.hero-section {
  background: linear-gradient(135deg, #1e293b 0%, #334155 100%);
  border-radius: 20px;
  padding: 48px;
  color: white;
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 32px;
  position: relative;
  overflow: hidden;
  box-shadow: 0 20px 25px -5px rgba(15, 23, 42, 0.1), 0 10px 10px -5px rgba(15, 23, 42, 0.04);
}

.hero-content {
  max-width: 600px;
  position: relative;
  z-index: 10;
}

.hero-title {
  font-size: 36px;
  font-weight: 800;
  margin: 16px 0;
  line-height: 1.2;
}

.hero-desc {
  font-size: 16px;
  color: #cbd5e1;
  line-height: 1.6;
  margin-bottom: 32px;
}

.hero-actions {
  display: flex;
  gap: 16px;
}

/* Illustration Placeholder */
.hero-illustration {
  position: relative;
  width: 200px;
  height: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.hero-icon {
  font-size: 120px;
  color: rgba(255,255,255,0.1);
  position: relative;
  z-index: 2;
}

.circle-decor {
  position: absolute;
  border-radius: 50%;
}

.c1 {
  width: 250px;
  height: 250px;
  background: radial-gradient(circle, rgba(99, 102, 241, 0.4) 0%, rgba(255,255,255,0) 70%);
  top: -20px;
  right: -20px;
}

.c2 {
  width: 150px;
  height: 150px;
  background: radial-gradient(circle, rgba(236, 72, 153, 0.4) 0%, rgba(255,255,255,0) 70%);
  bottom: 0;
  left: 0;
}

/* Stats Overview */
.stats-overview {
  margin-bottom: 32px;
}

.stats-card {
  border-radius: 16px;
  overflow: visible;
}

.stats-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.header-title {
  display: flex;
  align-items: center;
  gap: 12px;
}

.header-icon {
  font-size: 24px;
  color: #4f46e5;
  background: #eef2ff;
  padding: 8px;
  border-radius: 8px;
  width: auto;
  height: auto;
}

.header-title h3 {
  margin: 0;
  font-size: 18px;
  font-weight: 700;
  color: #0f172a;
}

.header-meta {
  display: flex;
  align-items: center;
  gap: 16px;
}

.filename {
  font-size: 13px;
  color: #64748b;
  display: flex;
  align-items: center;
  gap: 6px;
  background: #f1f5f9;
  padding: 4px 12px;
  border-radius: 6px;
}

.stats-content {
  display: grid;
  grid-template-columns: 1fr 1.5fr;
  gap: 32px;
  align-items: center;
}

.chart-section {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 250px;
}

.chart-container {
  width: 100%;
  height: 100%;
  position: relative;
}

.details-section {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.total-stat-box {
  background: linear-gradient(to right, #f8fafc, white);
  border: 1px solid #e2e8f0;
  border-radius: 12px;
  padding: 16px 24px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.total-stat-box .label {
  font-size: 14px;
  color: #64748b;
  font-weight: 500;
}

.total-stat-box .value {
  font-size: 24px;
  font-weight: 700;
  color: #0f172a;
}

.sentiment-bars {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.sentiment-item {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.item-header {
  display: flex;
  justify-content: space-between;
  font-size: 14px;
  font-weight: 600;
}

.text-success { color: #10b981; }
.text-danger { color: #ef4444; }
.text-warning { color: #f59e0b; }

.item-header .count {
  color: #64748b;
  font-size: 13px;
  font-weight: 500;
}

@media (max-width: 768px) {
  .stats-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 16px;
  }
  
  .header-meta {
    flex-wrap: wrap;
    width: 100%;
  }
  
  .stats-content {
    grid-template-columns: 1fr;
  }
  
  .chart-section {
    height: 200px;
  }
}

/* Info Grid */
.info-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 24px;
  margin-bottom: 32px;
}

.info-card {
  border-radius: 16px;
  border: 1px solid #e2e8f0;
  transition: transform 0.2s;
}

.info-card:hover {
  transform: translateY(-5px);
}

.card-icon {
  width: 48px;
  height: 48px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  color: white;
  margin-bottom: 20px;
}

.bg-indigo { background: linear-gradient(135deg, #6366f1, #4f46e5); }
.bg-rose { background: linear-gradient(135deg, #f43f5e, #e11d48); }
.bg-emerald { background: linear-gradient(135deg, #10b981, #059669); }

.info-card h3 {
  font-size: 18px;
  font-weight: 700;
  color: #0f172a;
  margin: 0 0 12px;
}

.info-card p {
  font-size: 14px;
  color: #64748b;
  line-height: 1.6;
  margin: 0;
}

/* System Info Footer */
.system-info {
  border-radius: 12px;
  background-color: white;
}

.info-row {
  display: flex;
  align-items: center;
  gap: 24px;
  justify-content: center;
}

.info-item {
  display: flex;
  align-items: center;
  gap: 12px;
}

.info-item .label {
  font-size: 13px;
  color: #94a3b8;
  font-weight: 500;
}

.info-item .value {
  font-size: 14px;
  font-weight: 600;
  color: #334155;
}

.status-ok {
  color: #10b981;
  display: flex;
  align-items: center;
  gap: 6px;
}

.dot {
  width: 8px;
  height: 8px;
  background-color: #10b981;
  border-radius: 50%;
  display: inline-block;
}

/* Responsive */
@media (max-width: 1024px) {
  .info-grid {
    grid-template-columns: 1fr;
  }
  .hero-section {
    flex-direction: column;
    text-align: center;
  }
  .hero-actions {
    justify-content: center;
  }
  .hero-illustration {
    display: none;
  }
}

@media (max-width: 768px) {
  .sidebar { display: none; }
  .info-row {
    flex-direction: column;
    gap: 12px;
    align-items: flex-start;
  }
  .el-divider--vertical {
    display: none;
  }
}
</style>
