<script setup>
import { ref, onMounted } from 'vue'
import { useI18n } from 'vue-i18n'
import Button from './Button.vue'

const { t } = useI18n()
const windowsDownloadUrl = ref('https://github.com/LanRhyme/MicYou/releases/latest')
const androidDownloadUrl = ref('https://github.com/LanRhyme/MicYou/releases/latest')
const apkDownloadUrl = ref('https://github.com/LanRhyme/MicYou/releases/latest')
const releaseVersion = ref('Latest')
const isLoading = ref(true)

const fetchReleaseData = async () => {
  try {
    const response = await fetch('https://api.github.com/repos/LanRhyme/MicYou/releases/latest')
    if (!response.ok) throw new Error('Failed to fetch')
    
    const data = await response.json()
    releaseVersion.value = data.tag_name
    
    const apkAsset = data.assets.find(asset => asset.name.endsWith('.apk'))
    const exeAsset = data.assets.find(asset => asset.name.endsWith('.exe') || asset.name.endsWith('.msi') || asset.name.endsWith('.zip'))
    
    const releasePage = data.html_url

    if (exeAsset) {
        windowsDownloadUrl.value = exeAsset.browser_download_url
    } else {
        windowsDownloadUrl.value = releasePage
    }

    if (apkAsset) {
        apkDownloadUrl.value = apkAsset.browser_download_url
    } else {
        apkDownloadUrl.value = releasePage
    }
    
    androidDownloadUrl.value = releasePage 
    
  } catch (error) {
    console.error('Error fetching release data', error)
  } finally {
    isLoading.value = false
  }
}

onMounted(() => {
  fetchReleaseData()
})
</script>

<template>
  <section id="download" class="download-section">
    <div class="download-content">
      <h2 class="section-title">{{ $t('download.title') }}</h2>
      <p class="section-subtitle">
        {{ $t('download.subtitle') }}
      </p>
      
      <div class="download-grid">
        <!-- Windows Card -->
        <div class="download-card">
          <div class="platform-icon">
             <span class="material-symbols-rounded">desktop_windows</span>
          </div>
          <h3 class="platform-name">{{ $t('download.winClient') }}</h3>
          <p class="platform-desc">{{ $t('download.winReq') }}</p>
          <Button 
            variant="filled" 
            :label="isLoading ? $t('download.loading') : $t('download.btnWinDownload')" 
            icon="download" 
            :href="windowsDownloadUrl" 
          />
          <div class="version-info">{{ releaseVersion }} • {{ $t('download.bit64') }}</div>
        </div>

        <!-- Android Card -->
        <div class="download-card">
          <div class="platform-icon">
             <span class="material-symbols-rounded">smartphone</span>
          </div>
          <h3 class="platform-name">{{ $t('download.androidApp') }}</h3>
          <p class="platform-desc">{{ $t('download.androidReq') }}</p>
          <div class="store-buttons">
             <Button variant="filled" :label="$t('download.btnGithub')" icon="shop" :href="androidDownloadUrl" />
             <Button variant="outlined" :label="$t('download.btnApk')" icon="android" :href="apkDownloadUrl" />
          </div>
          <div class="version-info">{{ releaseVersion }}</div>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.download-section {
  padding: 100px 24px;
  background-color: var(--md-sys-color-background);
  position: relative;
  overflow: hidden;
}

.download-content {
  max-width: 1000px;
  margin: 0 auto;
  text-align: center;
  position: relative;
  z-index: 2;
}

.section-title {
    font-size: 36px;
    font-weight: 700;
    margin-bottom: 16px;
    color: var(--md-sys-color-on-surface);
}

.section-subtitle {
    font-size: 18px;
    color: var(--md-sys-color-on-surface-variant);
    margin-bottom: 48px;
    max-width: 600px;
    margin-left: auto;
    margin-right: auto;
}

.download-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
  gap: 32px;
  justify-content: center;
}

.download-card {
  background-color: var(--md-sys-color-surface-variant);
  border-radius: 24px;
  padding: 40px;
  display: flex;
  flex-direction: column;
  align-items: center;
  border: 1px solid rgba(255, 255, 255, 0.05);
  transition: transform 0.3s ease;
}

.download-card:hover {
  transform: translateY(-8px);
  background-color: color-mix(in srgb, var(--md-sys-color-surface-variant), white 5%);
  box-shadow: var(--md-sys-elevation-level3);
}

.platform-icon {
  font-size: 48px;
  color: var(--md-sys-color-primary);
  margin-bottom: 24px;
  width: 80px;
  height: 80px;
  background-color: var(--md-sys-color-secondary-container);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.platform-name {
  font-size: 24px;
  font-weight: 700;
  margin-bottom: 8px;
  color: var(--md-sys-color-on-surface);
}

.platform-desc {
  color: var(--md-sys-color-on-surface-variant);
  margin-bottom: 32px;
}

.store-buttons {
  display: flex;
  flex-direction: column;
  gap: 12px;
  width: 100%;
}

.version-info {
  margin-top: 16px;
  font-size: 12px;
  color: var(--md-sys-color-outline);
}
</style>
