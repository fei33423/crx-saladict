<template>
<div class="container-fluid">
  <div class="word-page-nav">
    <h1 class="word-page-header">{{ i18n(`${id}_title`) }}</h1>
    <div class="btn-group">
      <label class="btn btn-default" :class="{active: isOnlyEng}">{{ i18n('wordpage_only_english') }}
        <input type="checkbox" v-model="isOnlyEng" class="hide" autocomplete="off">
      </label>
      <button type="button" class="btn btn-default" @click="showExportPanel">{{ i18n('wordpage_export') }}</button>
      <button type="button" class="btn btn-danger" @click="clearRecords">{{ i18n('wordpage_clear') }}</button>
    </div>
  </div>
  <div class="row">
    <div class="right-aside">
      <p class="text-center right-aside-row">{{ i18n(`${id}_wordcount`).replace('%s', wordCount) }}</p>
      <div class="dict-panel-wrap">
        <transition appear name="fadeup">
          <div class="dict-panel">
            <iframe class="saladict-frame"
              name="saladict-frame"
              frameBorder="0"
              :src="frameSource"
              :style="{height: panelHeight + 'px'}"
            ></iframe>
          </div>
        </transition>
      </div>
    </div>
    <div class="col-sm-7 wordpage-list-wrap" v-if="records.length > 0" ref="wordListWrap">
      <div class="row" v-for="(record, iRecord) in records" :key="record.date">
        <div class="col-sm-4 text-right">
          <p class="wordpage-item-title">{{ record.localeDate }}</p>
        </div>
        <div class="col-sm-4">
          <table class="table table-hover word-table" @click="searchSelection">
            <tbody>
              <tr v-for="(word, iWord) in record.data" :key="word.text" @mouseover="relocateWordAside($event, word, iRecord, iWord)">
                <td class="text-center">
                  <button type="button" class="close" @click="removeWord(record.setId, record.date, word.text, iRecord, iWord)">×</button>
                  {{ word.text }}
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
      <div class="row" v-if="pageCount > 1">
        <nav class="col-sm-6 col-sm-offset-6 text-center">
          <ul class="pagination">
            <li
              :class="{disabled: pageIndex === 0}"
              @click.prevent="pageIndex !== 0 && getPage(pageIndex - 1)"
            ><a href="#"><span>&laquo;</span></a></li>
            <li
              v-for="p in pageNumbers"
              :class="{active: p === pageIndex}"
              @click.prevent="p !== pageIndex && getPage(p)"
            ><a href="#">{{ p + 1 }}</a></li>
            <li
              :class="{disabled: pageIndex === pageCount - 1}"
              @click.prevent="pageIndex !== pageCount - 1 && getPage(pageIndex + 1)"
            ><a href="#"><span>&raquo;</span></a></li>
          </ul>
        </nav>
      </div>
      <div class="wordcard" ref="wordcard" v-if="wordcardData" @click="searchSelection" :style="{transform: `translateY(${wordcardTop}px)`}">
        <svg class="icon-edit" @click="openEditNotePanel" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 60 60">
          <path d="M36.72 22h-25c-.553 0-1 .448-1 1s.447 1 1 1h25c.55 0 1-.448 1-1s-.45-1-1-1zm-25-6h10c.55 0 1-.448 1-1s-.45-1-1-1h-10c-.553 0-1 .448-1 1s.447 1 1 1zm25 14h-25c-.553 0-1 .448-1 1s.447 1 1 1h25c.55 0 1-.448 1-1s-.45-1-1-1zm-8 8h-17c-.553 0-1 .448-1 1s.447 1 1 1h17c.55 0 1-.448 1-1s-.45-1-1-1zm-3 10c.55 0 1-.448 1-1s-.45-1-1-1h-14c-.553 0-1 .448-1 1s.447 1 1 1h14z"/>
          <path d="M2.72 2h29v14h14v18h2V14.586L33.132 0H.72v60h29v-2h-27V2zm31 1.414L44.304 14H33.72V3.414z"/>
          <path d="M58.407 37.162l-2.85-2.85c-1.127-1.128-3.093-1.128-4.22 0l-15.392 15.39-2.12 7.78-.52.518c-.388.388-.39 1.014-.006 1.405l-.008.02.02-.005c.194.19.446.287.698.287.257 0 .513-.098.708-.293l.518-.52 7.78-2.12 15.39-15.39c.563-.565.874-1.314.874-2.112s-.31-1.547-.875-2.11zm-20.153 13.06L48.497 39.98l4.243 4.242-10.243 10.243-4.243-4.243zm-.903 1.925l3.22 3.22-4.424 1.207 1.207-4.427zM56.996 39.97l-2.84 2.838-4.242-4.243 2.84-2.84c.372-.37 1.02-.372 1.393 0l2.85 2.85c.186.187.29.434.29.697-.002.263-.104.51-.29.697z"/>
        </svg>
        <div class="wordcard-section-wrap" :style="{height: wordcardTransHeight + 'px', overflow: wordcardTransHeight ? 'visible' : 'hidden'}">
          <transition name="fade">
            <section class="wordcard-section" ref="wordcardTrans" v-if="wordcardData.trans">
              <svg class="wordcard-section-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 469.333 469.333">
                <path d="M253.227 300.267L199.04 246.72l.64-.64c37.12-41.387 63.573-88.96 79.147-139.307h62.507V64H192V21.333h-42.667V64H0v42.453h238.293c-14.4 41.173-36.907 80.213-67.627 114.347-19.84-22.08-36.267-46.08-49.28-71.467H78.72c15.573 34.773 36.907 67.627 63.573 97.28l-108.48 107.2L64 384l106.667-106.667 66.347 66.347 16.213-43.413zM373.333 192h-42.667l-96 256h42.667l24-64h101.333l24 64h42.667l-96-256zm-56 149.333L352 248.853l34.667 92.48h-69.334z"/>
              </svg>
              <p>{{ wordcardData.trans }}</p>
            </section>
          </transition>
        </div>
        <div class="wordcard-section-wrap":style="{height: wordcardSenHeight + 'px', overflow: wordcardSenHeight ? 'visible' : 'hidden'}">
          <transition name="fade">
            <section class="wordcard-section" ref="wordcardSen" v-if="wordcardData.context">
              <svg class="wordcard-section-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 95.333 95.332">
                <path d="M 36.587 45.263 C 35.07 44.825 33.553 44.605 32.078 44.605 C 29.799 44.605 27.898 45.125 26.423 45.763 C 27.844 40.559 31.259 31.582 38.061 30.57 C 38.69 30.476 39.207 30.021 39.379 29.408 L 40.864 24.09 C 40.99 23.641 40.916 23.16 40.66 22.77 C 40.403 22.38 39.991 22.119 39.529 22.056 C 39.027 21.987 38.515 21.952 38.009 21.952 C 29.844 21.952 21.759 30.474 18.347 42.675 C 16.344 49.833 15.757 60.595 20.686 67.369 C 23.445 71.16 27.472 73.183 32.657 73.385 L 32.717 73.386 C 39.114 73.386 44.783 69.079 46.508 62.915 C 47.538 59.229 47.073 55.364 45.196 52.029 C 43.338 48.731 40.28 46.327 36.581 45.263 Z M 76.615 52.029 C 74.758 48.731 71.699 46.327 68.002 45.263 C 66.484 44.823 64.968 44.604 63.492 44.604 C 61.214 44.604 59.311 45.121 57.838 45.76 C 59.259 40.553 62.673 31.579 69.475 30.564 C 70.102 30.47 70.619 30.016 70.793 29.402 L 72.28 24.085 C 72.403 23.635 72.332 23.155 72.073 22.764 C 71.814 22.373 71.401 22.113 70.942 22.049 C 70.438 21.981 69.928 21.946 69.417 21.946 C 61.253 21.946 53.169 30.467 49.755 42.669 C 47.752 49.827 47.166 60.59 52.101 67.364 C 54.858 71.153 58.887 73.178 64.069 73.379 C 64.091 73.38 64.111 73.381 64.134 73.381 C 70.527 73.381 76.198 69.074 77.923 62.908 C 78.953 59.224 78.485 55.358 76.609 52.022 Z" style=""/>
              </svg>
              <p>{{ wordcardData.context }}</p>
            </section>
          </transition>
        </div>
        <div class="wordcard-section-wrap":style="{height: wordcardNoteHeight + 'px', overflow: wordcardNoteHeight ? 'visible' : 'hidden'}">
          <transition name="fade">
            <section class="wordcard-section" ref="wordcardNote" v-if="wordcardData.note">
              <svg class="wordcard-section-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 35.738 35.738">
                <path d="M0 35.667S11.596-1.403 35.738.117c0 0-2.994 4.85-10.55 6.416 0 0 3.517.43 6.368-.522 0 0-1.71 5.517-11.025 6.275 0 0 5.135 1.33 7.416.57 0 0-.62 4.11-10.102 6.154-.562.12-4.347 1.066-1.306 1.447 0 0 4.37.763 5.514.38 0 0-3.743 5.608-12.927 5.133-.903-.048-1.332 0-1.332 0L0 35.666z"/>
              </svg>
              <p class="wordcard-sec-note-content">{{ wordcardData.note }}</p>
            </section>
          </transition>
        </div>
        <hr>
        <footer class="wordcard-footer-wrap" :style="{height: wordcardFooterHeight + 'px'}">
          <transition name="fade">
            <div class="wordcard-footer" ref="wordcardFooter" v-if="wordcardData.title">
              <img :src="wordcardData.favicon" v-if="wordcardData.favicon" class="wordcard-favicon">
              <p class="wordcard-title">
                <a :href="wordcardData.url" @click.prevent="openURL(wordcardData.url)">{{ wordcardData.title }}</a>
              </p>
            </div>
          </transition>
        </footer>
      </div>
    </div>
    <div v-if="isReady && records.length <= 0" class="col-sm-7 wordpage-list-wrap no-record">
      <h1 class="no-wordpage-title">{{ i18n(`${id}_no_result`) }}</h1>
    </div>
  </div>

  <!--Export text-->
  <transition name="fade">
    <div class="modal show text-left" v-if="isShowExportPanel">
      <div class="modal-dialog exp-panel">
        <div class="modal-content">
          <div class="modal-header">
            <button type="button" class="close" @click="isShowExportPanel = false">&times;</button>
            <h4 class="modal-title">{{ i18n('wordpage_exp_modal_title') }}</h4>
          </div>
          <div class="modal-body exp-panel-body">
            <div class="exp-panel-pattern">
              <p>{{ i18n('wordpage_exp_description') }}</p>
              <table class="table table-bordered table-striped table-condensed">
                <colgroup>
                  <col class="col-xs-1">
                  <col class="col-xs-5">
                  <col class="col-xs-1">
                  <col class="col-xs-5">
                </colgroup>
                <thead>
                  <tr>
                    <th class="text-center">{{ i18n('wordpage_exp_placeholder') }}</th>
                    <th>{{ i18n('wordpage_exp_gencontent') }}</th>
                    <th class="text-center">{{ i18n('wordpage_exp_placeholder') }}</th>
                    <th>{{ i18n('wordpage_exp_gencontent') }}</th>
                  </tr>
                </thead>
                <tbody>
                  <tr>
                    <td class="text-center"><code>%text%</code></td>
                    <td>{{ i18n('edit_note_word') }}</td>
                    <td class="text-center"><code>%title%</code></td>
                    <td>{{ i18n('edit_note_src_title') }}</td>
                  </tr>
                  <tr>
                    <td class="text-center"><code>%context%</code></td>
                    <td>{{ i18n('edit_note_context') }}</td>
                    <td class="text-center"><code>%url%</code></td>
                    <td>{{ i18n('edit_note_src_url') }}</td>
                  </tr>
                  <tr>
                    <td class="text-center"><code>%note%</code></td>
                    <td>{{ i18n('edit_note_note') }}</td>
                    <td class="text-center"><code>%favicon%</code></td>
                    <td>{{ i18n('edit_note_src_favicon') }}</td>
                  </tr>
                  <tr>
                    <td class="text-center"><code>%trans%</code></td>
                    <td>{{ i18n('edit_note_trans') }}</td>
                    <td></td>
                    <td></td>
                  </tr>
                </tbody>
              </table>
              <textarea v-focus class="form-control exp-panel-pattern-textarea " v-model="expPattern"></textarea>
            </div>
            <textarea ref="exptext" class="form-control" readonly>{{ expText }}</textarea>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-default" @click="isShowExportPanel = false">{{ i18n('cancel') }}</button>
            <button type="button" class="btn btn-primary" @click="copyToClipBoard">{{ i18n('copy') }}</button>
            <button type="button" class="btn btn-success" @click="saveAsFile">{{ i18n('wordpage_savefile') }}</button>
          </div>
        </div>
      </div>
    </div>
  </transition> <!--modal-->

  <!--Edit Note-->
  <transition name="fade">
    <div class="modal show text-left" v-if="isShowEditNotePanel">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <button type="button" class="close" @click="closeEditNotePanel">&times;</button>
            <h4 class="modal-title">{{ i18n('wordpage_editnote_modal_title') }}</h4>
          </div>
          <div class="modal-body">
            <form>
              <div class="form-group">
                <div class="form-group">
                  <label for="note-word-text">{{ i18n('edit_note_word') }}</label>
                  <input class="form-control" placeholder="Word" id="note-word-text" v-model="wordcardData.text">
                </div>
              </div>
              <div class="form-group">
                <label for="note-word-trans">{{ i18n('edit_note_trans') }}</label>
                <textarea v-focus class="form-control" row="5" placeholder="Translation" id="note-word-trans" v-model="wordcardData.trans"></textarea>
              </div>
              <div class="form-group">
                <label for="note-word-note">{{ i18n('edit_note_note') }}</label>
                <textarea class="form-control" row="5" placeholder="Notes" id="note-word-note" v-model="wordcardData.note"></textarea>
              </div>
              <div class="form-group">
                <label for="note-word-context">{{ i18n('edit_note_context') }}</label>
                <textarea class="form-control" row="5" placeholder="Context" id="note-word-context" v-model="wordcardData.context"></textarea>
              </div>
              <div class="form-group">
                <label for="note-source-title">{{ i18n('edit_note_src_title') }}</label>
                <input class="form-control" placeholder="Source" id="note-source-title" v-model="wordcardData.title">
              </div>
              <div class="form-group">
                <label for="note-source-url">{{ i18n('edit_note_src_url') }}</label>
                <input class="form-control" placeholder="Source URL" id="note-source-url" v-model="wordcardData.url">
              </div>
              <div class="form-group">
                <label for="note-source-favicon">{{ i18n('edit_note_src_favicon') }}<img class="favicon-note-source" :src="wordcardData.favicon" v-if="wordcardData.favicon"></label>
                <input class="form-control" placeholder="Source Favicon" id="note-source-favicon" v-model="wordcardData.favicon">
              </div>
            </form>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-default" @click="closeEditNotePanel">{{ i18n('cancel') }}</button>
            <button type="button" class="btn btn-primary" @click="saveNote">{{ i18n('save') }}</button>
            <button type="button" class="btn btn-success" @click="saveNoteToNotebook" v-if="id !== 'notebook'">{{ i18n('save_to_notebook') }}</button>
          </div>
        </div>
      </div>
    </div>
  </transition> <!--modal-->

  <!--Alert Modal-->
  <alert-modal ref="alert" />

  <transition name="popup">
    <div class="popup-msg" :class="[popupType]" v-if="isPopUp">
      {{ popupMessage }}
    </div>
  </transition>
</div>
</template>

<script>
import {storage, message} from 'src/helpers/chrome-api'
import AlertModal from 'src/components/AlertModal'
import {addRecord} from 'src/helpers/record-manager'
import {isContainChinese, isContainEnglish} from 'src/helpers/lang-check'
import moment from 'moment'

let lang = chrome.i18n.getUILanguage().toLowerCase()
if (!/^(en|zh-cn|zh-tw|zh-hk)$/.test(lang)) {
  lang = 'en'
}
moment.locale(lang)

export default {
  props: {
    id: {type: String, require: true},
    config: {type: Object, require: true},
    downloadFileName: {type: String, require: true},
    i18n: {type: Function, required: true},
    recordManager: {type: Object, require: true}
  },
  data () {
    return {
      rawRecords: [],
      wordCount: 0,

      text: '',
      frameSource: chrome.runtime.getURL('panel.html'),

      pageIndex: 0,
      pageCount: 0,
      isMergeFirstTwo: false, // if the first page is too short, merge with the second one

      wordcardTop: 0,
      wordcardData: null,
      iActiveRecord: 0,
      iActiveWord: 0,

      wordcardTransHeight: 0,
      wordcardSenHeight: 0,
      wordcardNoteHeight: 0,
      wordcardFooterHeight: 0,

      isOnlyEng: false,

      isReady: false, // prevent blink

      rawAllwords: [],
      expPattern: '%text%',

      isShowExportPanel: false,
      isShowEditNotePanel: false,
      isPopUp: false,
      popupMessage: ''
    }
  },
  computed: {
    records () {
      if (!this.isOnlyEng) {
        return this.rawRecords
      }
      return this.rawRecords
        .map(record => ({
          // ...folder
          setId: record.setId,
          date: record.date,
          localeDate: record.localeDate,
          data: record.data
            .filter(word => !isContainChinese(word.text) && isContainEnglish(word.text))
        }))
        .filter(record => record.data.length > 0)
    },
    pageNumbers () {
      if (!this.pageCount) { return [] }
      if (this.pageCount < 5) {
        // near left end
        return Array.from(Array(this.pageCount)).map((x, i) => i)
      }
      if (this.pageCount - this.pageIndex < 5) {
        // near right end
        return Array.from(Array(5)).map((x, i) => this.pageCount - 5 + i)
      }
      return Array.from(Array(5)).map((x, i) => this.pageIndex + i)
    },
    expText () {
      const pattern = this.expPattern
      return this.rawAllwords
        .map(word =>
          pattern.replace(/%(\S+)%/g, (match, k) =>
            typeof word[k] === 'string' ? word[k] : match)
        )
        .join('\n')
    },
    panelHeight () {
      const allDicts = this.config.dicts.all
      // header + each dictionary
      const preferredHeight = 30 + this.config.dicts.selected.reduce((sum, id) => {
        let preferredHeight = 0
        if (allDicts[id] && allDicts[id].preferredHeight) {
          preferredHeight = allDicts[id].preferredHeight + 20
        }
        return sum + preferredHeight
      }, 0)
      const maxHeight = window.innerHeight * 0.78
      return preferredHeight > maxHeight ? maxHeight : preferredHeight
    }
  },
  methods: {
    getPage (index) {
      if (typeof index === 'number') {
        this.pageIndex = index
      } else {
        this.pageIndex += 1
      }

      let requestIndex = this.pageIndex
      if (requestIndex === 0) {
        this.isMergeFirstTwo = false
      } else if (this.isMergeFirstTwo) {
        requestIndex = this.pageIndex + 1
      }
      return this.recordManager.getRecordSet(requestIndex)
        .then(({recordSet, pageCount}) => {
          if (!recordSet) {
            this.rawRecords = []
            return
          }
          const rawRecords = recordSet.data
          rawRecords.forEach(record => {
            record.setId = recordSet.id
            record.localeDate = moment(record.date, 'MMDDYYYY').format('dddd LL')
          })
          if (requestIndex === 0 && recordSet.wordCount < 50 && pageCount >= 2) {
            this.isMergeFirstTwo = true
            // merge with the second set
            this.recordManager.getRecordSet(requestIndex + 1)
              .then(({recordSet, pageCount}) => {
                if (!recordSet) { return }
                recordSet.data.forEach(record => {
                  record.setId = recordSet.id
                  record.localeDate = moment(record.date, 'MMDDYYYY').format('dddd LL')
                })
                this.rawRecords = rawRecords.concat(recordSet.data)
                this.pageCount = pageCount - 1
              })
          } else {
            this.rawRecords = rawRecords
            this.pageCount = this.isMergeFirstTwo ? pageCount - 1 : pageCount
          }
        })
    },
    getWordCount () {
      this.recordManager.getWordCount()
        .then(wordCount => {
          this.wordCount = wordCount
        })
    },
    showExportPanel () {
      this.rawAllwords = []
      this.recordManager.getAllWords()
        .then(words => {
          this.rawAllwords = words
          this.isShowExportPanel = true
        })
    },
    saveAsFile () {
      const content = window.isWin
        ? this.expText.replace(/\r\n|\n/g, '\r\n')
        : this.expText
      const a = document.createElement('a')
      const file = new Blob([content], {type: 'text/plain;charset=utf-8'})
      a.href = URL.createObjectURL(file)
      a.download = this.downloadFileName
      a.click()
    },
    copyToClipBoard () {
      chrome.permissions.request({
        permissions: ['clipboardWrite']
      }, granted => {
        var isSuccess = false
        if (granted) {
          this.$refs.exptext.select()
          isSuccess = document.execCommand('copy')
          this.$refs.exptext.blur()
        }
        if (isSuccess) {
          this.popup(chrome.i18n.getMessage('wordpage_copy_success'), 'alert-success')
        } else {
          this.popup(chrome.i18n.getMessage('wordpage_copy_failed'), 'alert-danger')
        }
      })
    },
    clearRecords () {
      this.$refs.alert.$emit('show', {
        title: chrome.i18n.getMessage(`${this.id}_clear_modal_title`),
        content: chrome.i18n.getMessage(`${this.id}_clear_modal_content`),
        onConfirm: () => {
          this.recordManager.clearRecords()
            .then(() => this.getPage(0))
            .then(() => this.popup(chrome.i18n.getMessage('wordpage_clear_success'), 'alert-success'))
        }
      })
    },
    removeWord (setId, recordDate, text, iRecord, iWord) {
      this.recordManager.removeWord(setId, recordDate, text)
        .then(() => {
          this.rawRecords[iRecord].data.splice(iWord, 1)
          // reset aside card
          this.wordcardTop = 0
          this.wordcardData = null
          this.iActiveWord = 0
          this.iActiveRecord = 0
        })
    },
    searchSelection () {
      if (window.getSelection().toString().trim()) {
        // if user click on a selected text,
        // getSelection would return the text before it disappears
        // delay to wait for selection get cleared
        setTimeout(() => {
          const text = window.getSelection().toString().trim()
          if (text) {
            message.self.send({
              msg: 'SEARCH_TEXT',
              selectionInfo: {
                text,
                context: '',
                title: document.title,
                url: document.URL,
                favicon: chrome.runtime.getURL('assets/icon-16.png'),
                trans: '',
                note: ''
              }
            })
          }
        }, 0)
      }
    },
    relocateWordAside (event, word, iRecord, iWord) {
      const wordListWrap = this.$refs.wordListWrap
      var top = 0
      for (let node = event.currentTarget; node && node !== wordListWrap; node = node.offsetParent) {
        top += node.offsetTop
      }
      this.wordcardTop = top
      this.iActiveRecord = iRecord
      this.iActiveWord = iWord
      this.wordcardData = word
    },
    openEditNotePanel () {
      // backup value
      this.bakWordAsideData = JSON.stringify(this.wordcardData)
      this.isShowEditNotePanel = true
    },
    closeEditNotePanel () {
      // restore value
      this.wordcardData = JSON.parse(this.bakWordAsideData)
      this.isShowEditNotePanel = false
    },
    saveNote () {
      this.recordManager.saveWord({
        setId: this.records[this.iActiveRecord].setId,
        iRecord: this.iActiveRecord,
        iWord: this.iActiveWord,
        word: this.wordcardData
      }).then(() => {
        this.isShowEditNotePanel = false // close & not restore
        this.popup(chrome.i18n.getMessage('wordpage_save_success'), 'alert-success')
      }).catch(() => {
        this.popup(chrome.i18n.getMessage('wordpage_save_faild'), 'alert-danger')
      })
    },
    saveNoteToNotebook () {
      addRecord('notebook', this.wordcardData)
        .then(() => {
          this.closeEditNotePanel() // close & restore
          this.popup(chrome.i18n.getMessage('wordpage_save_success'), 'alert-success')
        }).catch(() => {
          this.popup(chrome.i18n.getMessage('wordpage_save_faild'), 'alert-danger')
        })
    },
    openURL (url) {
      if (url.startsWith('#')) { return }
      message.send({msg: 'OPEN_URL', url})
    },
    popup (msg, type) {
      if (msg) {
        clearTimeout(this.popupTimeout)
        this.popupMessage = msg
        this.isPopUp = true
        this.popupType = type || 'alert-info'
      }
    }
  },
  watch: {
    isPopUp (val) {
      if (val) {
        this.popupTimeout = setTimeout(() => {
          this.isPopUp = false
        }, 2000)
      }
    },
    records (newVal, oldVal) {
      // init wordcardData
      if (newVal.length > 0 && oldVal.length <= 0) {
        this.wordcardData = newVal[0].data[0]
      }
    },
    wordcardData: {
      deep: true,
      handler () {
        if (!this.wordcardData) { return }
        this.$nextTick(() => {
          this.wordcardTransHeight = this.$refs.wordcardTrans
            ? this.$refs.wordcardTrans.offsetHeight
            : 0
          this.wordcardSenHeight = this.$refs.wordcardSen
            ? this.$refs.wordcardSen.offsetHeight
            : 0
          this.wordcardNoteHeight = this.$refs.wordcardNote
            ? this.$refs.wordcardNote.offsetHeight
            : 0
          this.wordcardFooterHeight = this.$refs.wordcardFooter
            ? this.$refs.wordcardFooter.offsetHeight
            : 0
        })
      }
    },
    expPattern (expPattern) {
      storage.sync.set({expPattern})
    }
  },
  beforeCreate () {
    chrome.runtime.getPlatformInfo(({os}) => {
      window.isWin = os === 'win'
    })
  },
  created () {
    this.getWordCount()
    this.getPage(0)
      .then(() => {
        this.isReady = true
        this.recordManager.listenRecord(() => {
          this.getPage(0)
          this.getWordCount()
        })
      })

    storage.sync.get('expPattern')
      .then(({expPattern}) => {
        if (expPattern) {
          this.expPattern = expPattern
        }
      })
  },
  components: {
    AlertModal
  },
  directives: {
    focus: {
      inserted: function (el) {
        el.focus()
      }
    }
  }
}
</script>

<style lang="scss">
/*------------------------------------*\
   Vars
\*------------------------------------*/
$wordpage-nav-height: 50px;

/*------------------------------------*\
   Bootstrap
\*------------------------------------*/
@import "~bootstrap-sass/assets/stylesheets/bootstrap/variables";
@import "~bootstrap-sass/assets/stylesheets/bootstrap/mixins";

// Reset and dependencies
@import "~bootstrap-sass/assets/stylesheets/bootstrap/normalize";

// Core CSS
@import "~bootstrap-sass/assets/stylesheets/bootstrap/scaffolding";
@import "~bootstrap-sass/assets/stylesheets/bootstrap/type";
@import "~bootstrap-sass/assets/stylesheets/bootstrap/code";
@import "~bootstrap-sass/assets/stylesheets/bootstrap/grid";
@import "~bootstrap-sass/assets/stylesheets/bootstrap/tables";
@import "~bootstrap-sass/assets/stylesheets/bootstrap/forms";
@import "~bootstrap-sass/assets/stylesheets/bootstrap/buttons";

// Components
@import "~bootstrap-sass/assets/stylesheets/bootstrap/panels";
@import "~bootstrap-sass/assets/stylesheets/bootstrap/pagination";
@import "~bootstrap-sass/assets/stylesheets/bootstrap/close";
@import "~bootstrap-sass/assets/stylesheets/bootstrap/alerts";
@import "~bootstrap-sass/assets/stylesheets/bootstrap/button-groups";

// Components w/ JavaScript
@import "~bootstrap-sass/assets/stylesheets/bootstrap/modals";

/*------------------------------------*\
   Base
\*------------------------------------*/
html {
  overflow-y: scroll;
  scroll-behavior: smooth;
}

body {
  font-size: 14px;
  font-family: "Helvetica Neue", Helvetica, Arial, "Hiragino Sans GB", "Hiragino Sans GB W3", "Microsoft YaHei UI", "Microsoft YaHei", "WenQuanYi Micro Hei", sans-serif;
}

.modal-body {
  overflow-y: auto;
  max-height: 60vh;
}

/*------------------------------------*\
   Components
\*------------------------------------*/
.word-page-nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
  position: fixed;
  z-index: $zindex-navbar;
  top: 0;
  left: 0;
  right: 0;
  height: $wordpage-nav-height;
  padding: 0 10%;
  border-bottom: 1px #ddd solid;
  background-image: linear-gradient(white, white 30%, rgba(255, 255, 255, 0.82));
}

.word-page-header {
  font-size: 1.4em;
  margin: 10px 0;
}

.wordpage-list-wrap {
  position: relative;
  margin-top: $wordpage-nav-height;
  margin-bottom: 100vh;
}

.wordpage-list {
  overflow-x: hidden;
}

.wordpage-item-title {
  padding-top: 5px;
}

.wordcard {
  position: absolute;
  top: 0;
  right: 0;
  width: percentage(4/12);
  margin-top: 8px;
  padding: 30px 10px 10px;
  word-wrap: break-word;
  color: #8a6d3b;
  border: 1px #faebcc solid;
  border-radius: 20px;
  border-top-left-radius: 0;
  background: #fcf8e3;
  transition: all 0.4s;
  will-change: transform, height;

  hr {
    margin: 8px;
    border-color: #faebcc;
  }
}

.icon-edit {
  position: absolute;
  top: 10px;
  right: 10px;
  width: 18px;
  height: 18px;
  fill: #8a6d3b;
  cursor: pointer;
}

.wordcard-section-wrap {
  overflow: hidden;
  margin: 10px 0;
  transition: all 0.4s;
  will-change: height;
}

.wordcard-section-icon {
  position: absolute;
  top: -5px;
  left: 0;
  width: 18px;
  height: 18px;
  fill: #8a6d3b;
  user-select: none;
}

.wordcard-section {
  position: relative;
  padding: 0 10px 0 25px;
}

.wordcard-sec-note-content {
  padding: 10px;
  border: 1px dashed;
}

.wordcard-footer-wrap {
  overflow: hidden;
  transition: all 0.4s;
}

.wordcard-footer {
  padding: 0 10px;
}

.wordcard-favicon {
  float: left;
  margin-right: 5px;
  width: 14px;
  height: 14px;
}

.wordcard-title {
  overflow: hidden;
  margin: 0;
  font-size: 12px;

  a:link,
  a:visited,
  a:hover,
  a:active {
    color: #c39f5f;
    text-decoration: none;
  }
}

.no-record {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.no-wordpage-title {
  font-size: 1.5em;
}

.right-aside {
  position: fixed;
  top: 0;
  right: 0;
  display: flex;
  flex-direction: column;
  width: percentage(5/12);
  height: 100%;
  padding: $wordpage-nav-height 15px 0;
}

.right-aside-row {
  margin-top: 15px;
}

.dict-panel-wrap {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
}

.dict-panel {
  width: 400px;
}

.saladict-frame {
  width: 400px;
  overflow: hidden;
  border: 0 none;
  box-shadow: rgba(0, 0, 0, 0.8) 0px 4px 23px -6px;
  transition: all 1s;
}

.word-table {
  margin-bottom: 0;

  .close {
    opacity: 0;
  }

  tr:hover .close {
    opacity: 0.2;
  }
}

.word-td {
  padding-left: 15px 0 5px;
}

.exp-panel-body {
  display: flex;
  height: 55vh;

  > * {
    flex: 1;
  }
}

.exp-panel-pattern {
  margin-right: 15px;
  display: flex;
  flex-direction: column;
}

.exp-panel-pattern-textarea {
  flex: 1;
}

.popup-msg {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: $global-zindex-popover;
  padding: 20px 10px;
  font-size: 18px;
  font-weight: bold;
  text-align: center;
  border-top: 2px dashed currentColor;
}

.favicon-note-source {
  height: 16px;
  margin-left: 5px;
  vertical-align: text-bottom;
}

.exp-panel {
  width: 88%;
  max-width: 1200px;
}

/*------------------------------------*\
   States
\*------------------------------------*/
// Utility classes
@import "~bootstrap-sass/assets/stylesheets/bootstrap/utilities";

.fade-enter-active, .fade-leave-active {
  transition: opacity 500ms;
}
.fade-enter, .fade-leave-active {
  opacity: 0;
}

.popup-enter-active, .popup-leave-active {
  transition: transform 500ms;
}
.popup-enter, .popup-leave-active {
  transform: translateY(100%);
}

.fadeup-enter-active {
  transition: all 1.5s 1s;
}
.fadeup-enter {
  opacity: 0;
  transform: translate3d(0px, 40px, 0px);
}
</style>
