<template>
  <Modal @close="$emit('close')">
    <h3 slot="header">設定</h3>
    <div slot="body">
      <ul class="list">
        <li>
          夜間模式 <Toggle :on="nightMode" @change="handleToggleClick('nightMode')" />
        </li>
        <li>
          自動載入圖片 <Toggle :on="autoLoadImage" @change="handleToggleClick('autoLoadImage')" />
        </li>
        <li>
          節省圖片流量 <Toggle :on="imageProxy" @change="handleToggleClick('imageProxy')" />
        </li>
        <li>
          Youtube 預覽 <Toggle :on="youtubePreview" @change="handleToggleClick('youtubePreview')" />
        </li>
        <li>
          公司模式 <Toggle :on="officeMode" @change="handleToggleClick('officeMode')" />
        </li>
        <li>
          靜態表情 <Toggle :on="staticIcons" @change="handleToggleClick('staticIcons')" />
        </li>
        <li class="center">
          <ActionButton @click="handleClearHistory" :success="clearHistorySuccess">
            <span slot="text">
              <span class="icon-trash"></span> 清除瀏覽記錄
            </span>
            <span slot="successText">
              成功！
            </span>
          </ActionButton>
        </li>
      </ul>
      <div class="footer">
        <small>Made in HK by <a href="https://colloque.io/" target="_blank">Coke_Zero</a></small>
      </div>
    </div>
  </Modal>
</template>

<script>
import { mapState, mapMutations, mapActions } from 'vuex'

import helper from '@/helper'
import Modal from './Modal'
import Toggle from './Toggle'
import ActionButton from './ActionButton'

export default {
  components: {
    Modal,
    Toggle,
    ActionButton,
  },
  data() {
    return {
      clearHistorySuccess: false,
    }
  },
  computed: {
    ...mapState({
      officeMode: state => state.app.officeMode,
      staticIcons: state => state.app.staticIcons,
      autoLoadImage: state => state.app.autoLoadImage,
      nightMode: state => state.app.nightMode,
      youtubePreview: state => state.app.youtubePreview,
      imageProxy: state => state.app.imageProxy,
    }),
  },
  methods: {
    ...mapActions(['tryClearHistory']),
    ...mapMutations({
      toggleSettings: 'TOGGLE_SETTINGS',
      clearHistory: 'CLEAR_HISTORY',
    }),
    handleToggleClick(key) {
      helper.trackEvent({
        eventCategory: 'Settings',
        eventAction: 'click',
        eventLabel: `${key} ${this[key] ? 'OFF' : 'ON'}`,
      })
      this.toggleSettings(key)
    },
    async handleClearHistory() {
      helper.trackEvent({
        eventCategory: 'Settings',
        eventAction: 'click',
        eventLabel: 'Clear History',
      })
      try {
        this.clearHistory()
        this.clearHistorySuccess = true
        setTimeout(() => {
          this.clearHistorySuccess = false
        }, 2000)
      } catch (error) {
        console.log(error)
      }
    },
  },
}
</script>

<style lang="scss" scoped>
.list {
  list-style: none;
  padding: 0;
  margin: 0;

  li {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 1rem;

    &.center {
      justify-content: center;
    }
  }

  .subtitle {
    font-size: 0.75rem;
    color: #999;
  }
}

.footer {
  text-align: center;
  color: #999;
}
</style>
