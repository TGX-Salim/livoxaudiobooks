<template>
  <div>
    <app-settings-content :header-text="$strings.HeaderSettings">
      <div class="lg:flex">
        <div class="flex-1">
          <div class="pt-4">
            <h2 class="font-semibold">{{ $strings.HeaderSettingsGeneral }}</h2>
          </div>
          <div role="article" :aria-label="$strings.LabelSettingsStoreCoversWithItemHelp" class="flex items-end py-2">
            <ui-toggle-switch :label="$strings.LabelSettingsStoreCoversWithItem" v-model="newServerSettings.storeCoverWithItem" :disabled="updatingServerSettings" @input="(val) => updateSettingsKey('storeCoverWithItem', val)" />
            <ui-tooltip aria-hidden="true" :text="$strings.LabelSettingsStoreCoversWithItemHelp">
              <p class="pl-4">
                <span id="settings-store-cover-with-items">{{ $strings.LabelSettingsStoreCoversWithItem }}</span>
                <span class="material-symbols icon-text">info</span>
              </p>
            </ui-tooltip>
          </div>

          <div role="article" :aria-label="$strings.LabelSettingsStoreMetadataWithItemHelp" class="flex items-center py-2">
            <ui-toggle-switch :label="$strings.LabelSettingsStoreMetadataWithItem" v-model="newServerSettings.storeMetadataWithItem" :disabled="updatingServerSettings" @input="(val) => updateSettingsKey('storeMetadataWithItem', val)" />
            <ui-tooltip aria-hidden="true" :text="$strings.LabelSettingsStoreMetadataWithItemHelp">
              <p class="pl-4">
                <span id="settings-store-metadata-with-items">{{ $strings.LabelSettingsStoreMetadataWithItem }}</span>
                <span class="material-symbols icon-text">info</span>
              </p>
            </ui-tooltip>
          </div>

          <div role="article" :aria-label="$strings.LabelSettingsSortingIgnorePrefixesHelp" class="flex items-center py-2">
            <ui-toggle-switch :label="$strings.LabelSettingsSortingIgnorePrefixes" v-model="newServerSettings.sortingIgnorePrefix" :disabled="updatingServerSettings" @input="(val) => updateSettingsKey('sortingIgnorePrefix', val)" />
            <ui-tooltip aria-hidden="true" :text="$strings.LabelSettingsSortingIgnorePrefixesHelp">
              <p class="pl-4">
                <span id="settings-sorting-ignore-prefixes">{{ $strings.LabelSettingsSortingIgnorePrefixes }}</span>
                <span class="material-symbols icon-text">info</span>
              </p>
            </ui-tooltip>
          </div>
          <div v-if="newServerSettings.sortingIgnorePrefix" class="w-72 ml-14 mb-2">
            <ui-multi-select v-model="newServerSettings.sortingPrefixes" small :items="newServerSettings.sortingPrefixes" :label="$strings.LabelPrefixesToIgnore" @input="sortingPrefixesUpdated" :disabled="savingPrefixes" />
            <div class="flex justify-end py-1">
              <ui-btn v-if="hasPrefixesChanged" color="success" :loading="savingPrefixes" small @click="updateSortingPrefixes">Save</ui-btn>
            </div>
          </div>

          <div class="pt-4">
            <h2 class="font-semibold">{{ $strings.HeaderSettingsScanner }}</h2>
          </div>

          <div role="article" :aria-label="$strings.LabelSettingsParseSubtitlesHelp" class="flex items-center py-2">
            <ui-toggle-switch :label="$strings.LabelSettingsParseSubtitles" v-model="newServerSettings.scannerParseSubtitle" :disabled="updatingServerSettings" @input="(val) => updateSettingsKey('scannerParseSubtitle', val)" />
            <ui-tooltip aria-hidden="true" :text="$strings.LabelSettingsParseSubtitlesHelp">
              <p class="pl-4">
                <span id="settings-parse-subtitles">{{ $strings.LabelSettingsParseSubtitles }}</span>
                <span class="material-symbols icon-text">info</span>
              </p>
            </ui-tooltip>
          </div>

          <div role="article" :aria-label="$strings.LabelSettingsFindCoversHelp" class="flex items-center py-2">
            <ui-toggle-switch :label="$strings.LabelSettingsFindCovers" v-model="newServerSettings.scannerFindCovers" :disabled="updatingServerSettings" @input="(val) => updateSettingsKey('scannerFindCovers', val)" />
            <ui-tooltip aria-hidden="true" :text="$strings.LabelSettingsFindCoversHelp">
              <p class="pl-4">
                <span id="settings-find-covers">{{ $strings.LabelSettingsFindCovers }}</span>
                <span class="material-symbols icon-text">info</span>
              </p>
            </ui-tooltip>
            <div class="flex-grow" />
          </div>
          <div v-if="newServerSettings.scannerFindCovers" class="w-44 ml-14 mb-2">
            <ui-dropdown v-model="newServerSettings.scannerCoverProvider" small :items="providers" :label="$strings.LabelCoverProvider" @input="updateScannerCoverProvider" :disabled="updatingServerSettings" />
          </div>

          <div role="article" :aria-label="$strings.LabelSettingsPreferMatchedMetadataHelp" class="flex items-center py-2">
            <ui-toggle-switch :label="$strings.LabelSettingsPreferMatchedMetadata" v-model="newServerSettings.scannerPreferMatchedMetadata" :disabled="updatingServerSettings" @input="(val) => updateSettingsKey('scannerPreferMatchedMetadata', val)" />
            <ui-tooltip aria-hidden="true" :text="$strings.LabelSettingsPreferMatchedMetadataHelp">
              <p class="pl-4">
                <span id="settings-prefer-matched-metadata">{{ $strings.LabelSettingsPreferMatchedMetadata }}</span>
                <span class="material-symbols icon-text">info</span>
              </p>
            </ui-tooltip>
          </div>

          <div role="article" :aria-label="$strings.LabelSettingsEnableWatcherHelp" class="flex items-center py-2">
            <ui-toggle-switch :label="$strings.LabelSettingsEnableWatcher" v-model="scannerEnableWatcher" :disabled="updatingServerSettings" @input="(val) => updateSettingsKey('scannerDisableWatcher', !val)" />
            <ui-tooltip aria-hidden="true" :text="$strings.LabelSettingsEnableWatcherHelp">
              <p class="pl-4">
                <span id="settings-disable-watcher">{{ $strings.LabelSettingsEnableWatcher }}</span>
                <span class="material-symbols icon-text">info</span>
              </p>
            </ui-tooltip>
          </div>

          <div class="pt-4">
            <h2 class="font-semibold">{{ $strings.HeaderSettingsWebClient }}</h2>
          </div>

          <div class="flex items-center py-2">
            <ui-toggle-switch v-model="newServerSettings.chromecastEnabled" :label="$strings.LabelSettingsChromecastSupport" :disabled="updatingServerSettings" @input="(val) => updateSettingsKey('chromecastEnabled', val)" />
            <p aria-hidden="true" class="pl-4">{{ $strings.LabelSettingsChromecastSupport }}</p>
          </div>

          <div class="flex items-center py-2 mb-2">
            <ui-toggle-switch v-model="newServerSettings.allowIframe" :label="$strings.LabelSettingsAllowIframe" :disabled="updatingServerSettings" @input="(val) => updateSettingsKey('allowIframe', val)" />
            <p aria-hidden="true" class="pl-4">{{ $strings.LabelSettingsAllowIframe }}</p>
          </div>
        </div>

        <div class="flex-1">
          <div class="pt-4">
            <h2 class="font-semibold">{{ $strings.HeaderSettingsDisplay }}</h2>
          </div>

          <div class="flex items-center py-2">
            <ui-toggle-switch labeledBy="settings-home-page-uses-bookshelf" v-model="homepageUseBookshelfView" :disabled="updatingServerSettings" @input="updateHomeUseBookshelfView" />
            <ui-tooltip :text="$strings.LabelSettingsBookshelfViewHelp">
              <p class="pl-4">
                <span id="settings-home-page-uses-bookshelf">{{ $strings.LabelSettingsHomePageBookshelfView }}</span>
                <span class="material-symbols icon-text">info</span>
              </p>
            </ui-tooltip>
          </div>

          <div class="flex items-center py-2">
            <ui-toggle-switch labeledBy="settings-library-uses-bookshelf" v-model="useBookshelfView" :disabled="updatingServerSettings" @input="updateUseBookshelfView" />
            <ui-tooltip :text="$strings.LabelSettingsBookshelfViewHelp">
              <p class="pl-4">
                <span id="settings-library-uses-bookshelf">{{ $strings.LabelSettingsLibraryBookshelfView }}</span>
                <span class="material-symbols icon-text">info</span>
              </p>
            </ui-tooltip>
          </div>

          <div class="flex-grow py-2">
            <ui-dropdown :label="$strings.LabelSettingsDateFormat" v-model="newServerSettings.dateFormat" :items="dateFormats" small class="max-w-52" @input="(val) => updateSettingsKey('dateFormat', val)" />
            <p class="text-xs ml-1 text-white text-opacity-60">{{ $strings.LabelExample }}: {{ dateExample }}</p>
          </div>

          <div class="flex-grow py-2">
            <ui-dropdown :label="$strings.LabelSettingsTimeFormat" v-model="newServerSettings.timeFormat" :items="timeFormats" small class="max-w-52" @input="(val) => updateSettingsKey('timeFormat', val)" />
            <p class="text-xs ml-1 text-white text-opacity-60">{{ $strings.LabelExample }}: {{ timeExample }}</p>
          </div>

          <div class="py-2">
            <ui-dropdown :label="$strings.LabelLanguageDefaultServer" ref="langDropdown" v-model="newServerSettings.language" :items="$languageCodeOptions" small class="max-w-52" @input="updateServerLanguage" />
          </div>

          <!-- old experimental features -->
          <!-- <div class="pt-4">
            <h2 class="font-semibold">{{ $strings.HeaderSettingsExperimental }}</h2>
          </div>

          <div class="flex items-center py-2">
            <ui-toggle-switch labeledBy="settings-experimental-features" v-model="showExperimentalFeatures" />
            <ui-tooltip :text="$strings.LabelSettingsExperimentalFeaturesHelp">
              <p class="pl-4">
                <span id="settings-experimental-features">{{ $strings.LabelSettingsExperimentalFeatures }}</span>
                <a :aria-label="$strings.LabelSettingsExperimentalFeaturesHelp" href="https://github.com/advplyr/audiobookshelf/discussions/75" target="_blank">
                  <span class="material-symbols icon-text">info</span>
                </a>
              </p>
            </ui-tooltip>
          </div> -->
        </div>
      </div>
    </app-settings-content>

    <div class="h-0.5 bg-primary bg-opacity-30 w-full" />

    <div class="flex items-center py-4">
      <div class="flex-grow" />
      <ui-btn color="bg" small :padding-x="4" class="mr-2 text-xs md:text-sm" :loading="isPurgingCache" @click.stop="purgeCache">{{ $strings.ButtonPurgeAllCache }}</ui-btn>
      <ui-btn color="bg" small :padding-x="4" class="mr-2 text-xs md:text-sm" :loading="isPurgingCache" @click.stop="purgeItemsCache">{{ $strings.ButtonPurgeItemsCache }}</ui-btn>
    </div>

    <div class="flex items-center py-4">
      <div class="flex-grow" />
      <p class="pr-2 text-sm text-[#EF8036]">
        A Fork of audiobookshelf by TGX & Salim
      </p>
    </div>

    <div class="h-0.5 bg-primary bg-opacity-30 w-full" />

    <!-- confirm cache purge dialog -->
    <prompt-dialog v-model="showConfirmPurgeCache" :width="675">
      <div class="px-4 w-full text-sm py-6 rounded-lg bg-bg shadow-lg border border-black-300">
        <p class="text-error font-semibold">{{ $strings.MessageImportantNotice }}</p>
        <p class="my-8 text-center" v-html="$strings.MessageConfirmPurgeCache" />
        <div class="flex px-1 items-center">
          <ui-btn color="primary" @click="showConfirmPurgeCache = false">{{ $strings.ButtonNevermind }}</ui-btn>
          <div class="flex-grow" />
          <ui-btn color="success" @click="confirmPurge">{{ $strings.ButtonYes }}</ui-btn>
        </div>
      </div>
    </prompt-dialog>
  </div>
</template>

<script>
export default {
  asyncData({ store, redirect }) {
    if (!store.getters['user/getIsAdminOrUp']) {
      redirect('/')
    }
  },
  data() {
    return {
      isResettingLibraryItems: false,
      updatingServerSettings: false,
      homepageUseBookshelfView: false,
      useBookshelfView: false,
      scannerEnableWatcher: false,
      isPurgingCache: false,
      hasPrefixesChanged: false,
      newServerSettings: {},
      showConfirmPurgeCache: false,
      savingPrefixes: false
    }
  },
  watch: {
    serverSettings(newVal, oldVal) {
      if (newVal && !oldVal) {
        this.initServerSettings()
      }
    }
  },
  computed: {
    serverSettings() {
      return this.$store.state.serverSettings
    },
    providers() {
      return this.$store.state.scanners.providers
    },
    dateFormats() {
      return this.$store.state.globals.dateFormats
    },
    timeFormats() {
      return this.$store.state.globals.timeFormats
    },
    dateExample() {
      const date = new Date(2014, 2, 25)
      return this.$formatJsDate(date, this.newServerSettings.dateFormat)
    },
    timeExample() {
      const date = new Date(2014, 2, 25, 17, 30, 0)
      return this.$formatJsTime(date, this.newServerSettings.timeFormat)
    }
  },
  methods: {
    sortingPrefixesUpdated(val) {
      const prefixes = [...new Set(val?.map((prefix) => prefix.trim().toLowerCase()) || [])]
      this.newServerSettings.sortingPrefixes = prefixes
      const serverPrefixes = this.serverSettings.sortingPrefixes || []
      this.hasPrefixesChanged = prefixes.some((p) => !serverPrefixes.includes(p)) || serverPrefixes.some((p) => !prefixes.includes(p))
    },
    updateSortingPrefixes() {
      const prefixes = [...new Set(this.newServerSettings.sortingPrefixes.map((prefix) => prefix.trim().toLowerCase()) || [])]
      if (!prefixes.length) {
        this.$toast.error(this.$strings.ToastSortingPrefixesEmptyError)
        return
      }

      this.savingPrefixes = true
      this.$axios
        .$patch(`/api/sorting-prefixes`, { sortingPrefixes: prefixes })
        .then((data) => {
          this.$toast.success(this.$getString('ToastSortingPrefixesUpdateSuccess', [data.rowsUpdated]))
          if (data.serverSettings) {
            this.$store.commit('setServerSettings', data.serverSettings)
          }
          this.hasPrefixesChanged = false
        })
        .catch((error) => {
          console.error('Failed to update prefixes', error)
          this.$toast.error(this.$strings.ToastFailedToUpdate)
        })
        .finally(() => {
          this.savingPrefixes = false
        })
    },
    updateScannerCoverProvider(val) {
      this.updateServerSettings({
        scannerCoverProvider: val
      })
    },
    updateHomeUseBookshelfView(val) {
      this.updateServerSettings({
        homeBookshelfView: !val ? this.$constants.BookshelfView.DETAIL : this.$constants.BookshelfView.STANDARD
      })
    },
    updateUseBookshelfView(val) {
      this.updateServerSettings({
        bookshelfView: !val ? this.$constants.BookshelfView.DETAIL : this.$constants.BookshelfView.STANDARD
      })
    },
    updateServerLanguage(val) {
      this.updateSettingsKey('language', val)
    },
    updateSettingsKey(key, val) {
      if (key === 'scannerDisableWatcher') {
        this.newServerSettings.scannerDisableWatcher = val
      }
      this.updateServerSettings({
        [key]: val
      })
    },
    updateServerSettings(payload) {
      this.updatingServerSettings = true
      this.$store.dispatch('updateServerSettings', payload).then((response) => {
        this.updatingServerSettings = false

        if (response.error) {
          console.error('Failed to update server settins', response.error)
          this.$toast.error(response.error)
          this.initServerSettings()
          return
        }

        if (payload.language) {
          // Updating language after save allows for re-rendering
          this.$setLanguageCode(payload.language)
        }
      })
    },
    initServerSettings() {
      this.newServerSettings = this.serverSettings ? { ...this.serverSettings } : {}
      this.newServerSettings.sortingPrefixes = [...(this.newServerSettings.sortingPrefixes || [])]
      this.scannerEnableWatcher = !this.newServerSettings.scannerDisableWatcher

      this.homepageUseBookshelfView = this.newServerSettings.homeBookshelfView != this.$constants.BookshelfView.DETAIL
      this.useBookshelfView = this.newServerSettings.bookshelfView != this.$constants.BookshelfView.DETAIL
    },
    purgeCache() {
      this.showConfirmPurgeCache = true
    },
    async confirmPurge() {
      this.showConfirmPurgeCache = false
      this.isPurgingCache = true
      await this.$axios
        .$post('/api/cache/purge')
        .then(() => {
          this.$toast.success(this.$strings.ToastCachePurgeSuccess)
        })
        .catch((error) => {
          console.error('Failed to purge cache', error)
          this.$toast.error(this.$strings.ToastCachePurgeFailed)
        })
      this.isPurgingCache = false
    },
    purgeItemsCache() {
      const payload = {
        // message: `This will delete the entire folder at <code>/metadata/cache/items</code>.<br />Are you sure you want to purge items cache?`,
        message: this.$strings.MessageConfirmPurgeItemsCache,
        callback: (confirmed) => {
          if (confirmed) {
            this.sendPurgeItemsCache()
          }
        },
        type: 'yesNo'
      }
      this.$store.commit('globals/setConfirmPrompt', payload)
    },
    async sendPurgeItemsCache() {
      this.isPurgingCache = true
      await this.$axios
        .$post('/api/cache/items/purge')
        .then(() => {
          this.$toast.success(this.$strings.ToastCachePurgeSuccess)
        })
        .catch((error) => {
          console.error('Failed to purge items cache', error)
          this.$toast.error(this.$strings.ToastCachePurgeFailed)
        })
      this.isPurgingCache = false
    }
  },
  mounted() {
    this.initServerSettings()
  }
}
</script>
