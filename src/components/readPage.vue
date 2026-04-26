<template>
  <div class="min-h-screen bg-gradient-to-br from-slate-50 to-slate-100 flex flex-col items-center justify-center p-6">
    <!-- Header -->
    <div class="mb-10 text-center">
      <h1 class="text-4xl font-bold tracking-tight text-slate-900">LectureRapide</h1>
      <p class="mt-2 text-slate-500 text-sm">Lecture rapide par affichage séquentiel de mots</p>
    </div>

    <!-- Main card -->
    <div class="w-full max-w-lg bg-white rounded-2xl shadow-xl border border-slate-200 overflow-hidden">

      <!-- Word display area -->
      <div class="flex items-center justify-center h-48 bg-slate-900 px-8">
        <p
          v-if="wordToShow"
          class="text-4xl font-bold text-white tracking-wide transition-all duration-100"
        >
          {{ wordToShow }}
        </p>
        <p v-else class="text-slate-400 text-lg italic">
          {{ hasText ? 'Appuyez sur lecture...' : 'Chargez un fichier PDF' }}
        </p>
      </div>

      <!-- Controls -->
      <div class="p-6 space-y-6">

        <!-- File upload -->
        <div class="space-y-2">
          <label class="text-xs font-semibold text-slate-500 uppercase tracking-wider">Fichier PDF</label>
          <label
            class="flex items-center gap-3 w-full cursor-pointer rounded-lg border-2 border-dashed border-slate-300 px-4 py-3 hover:border-slate-400 hover:bg-slate-50 transition-colors"
          >
            <FileText class="h-5 w-5 text-slate-400 shrink-0" />
            <span class="text-sm text-slate-600 truncate">{{ fileName || 'Choisir un fichier PDF...' }}</span>
            <input
              type="file"
              ref="myFiles"
              accept="application/pdf"
              @change="previewFiles"
              class="hidden"
            />
          </label>
        </div>

        <!-- Speed control -->
        <div class="space-y-2">
          <label class="text-xs font-semibold text-slate-500 uppercase tracking-wider">Vitesse de lecture</label>
          <div class="flex items-center gap-3">
            <button
              @click="wordPerMin = Math.max(50, wordPerMin - 50)"
              class="inline-flex items-center justify-center rounded-lg h-9 w-9 border border-slate-200 bg-white hover:bg-slate-50 transition-colors text-slate-600"
            >
              <Minus class="h-4 w-4" />
            </button>
            <div class="flex-1 text-center">
              <span class="text-2xl font-bold text-slate-900">{{ wordPerMin }}</span>
              <span class="ml-1 text-sm text-slate-400">mots/min</span>
            </div>
            <button
              @click="wordPerMin += 50"
              class="inline-flex items-center justify-center rounded-lg h-9 w-9 border border-slate-200 bg-white hover:bg-slate-50 transition-colors text-slate-600"
            >
              <Plus class="h-4 w-4" />
            </button>
          </div>
          <!-- Speed bar indicator -->
          <div class="h-1.5 bg-slate-100 rounded-full overflow-hidden">
            <div
              class="h-full bg-slate-900 rounded-full transition-all duration-300"
              :style="{ width: Math.min((wordPerMin / 1000) * 100, 100) + '%' }"
            ></div>
          </div>
        </div>

        <!-- Play/Pause and Reset -->
        <div class="flex items-center gap-3 pt-2">
          <button
            @click="togglePlay"
            :disabled="!hasText || wordPerMin === 0"
            class="flex-1 inline-flex items-center justify-center gap-2 rounded-xl h-12 font-semibold text-white transition-all"
            :class="pause
              ? 'bg-slate-900 hover:bg-slate-800'
              : 'bg-red-500 hover:bg-red-600'"
          >
            <Play v-if="pause" class="h-5 w-5" />
            <Pause v-else class="h-5 w-5" />
            <span>{{ pause ? 'Lecture' : 'Pause' }}</span>
          </button>

          <button
            @click="resetReading"
            class="inline-flex items-center justify-center gap-2 rounded-xl h-12 px-5 border border-slate-200 bg-white hover:bg-slate-50 text-slate-600 font-semibold transition-colors"
          >
            <RotateCcw class="h-4 w-4" />
            <span>Reset</span>
          </button>
        </div>

        <!-- Progress info -->
        <div v-if="wordsArray.length > 0" class="text-center text-xs text-slate-400">
          Mot {{ currentWord + 1 }} / {{ wordsArray.length }}
          <span class="mx-2">·</span>
          ~{{ wordPerMin > 0 ? Math.ceil((wordsArray.length - currentWord) / wordPerMin) : '—' }} min restantes
        </div>

      </div>
    </div>

    <!-- Footer -->
    <p class="mt-8 text-xs text-slate-400">Par Zacharie DOS SANTOS</p>
  </div>
</template>

<script>
import { FileText, Play, Pause, RotateCcw, Minus, Plus } from 'lucide-vue-next'
const pdfjsLib = require("pdfjs-dist");
pdfjsLib.GlobalWorkerOptions.workerSrc = `//cdnjs.cloudflare.com/ajax/libs/pdf.js/2.15.349/pdf.worker.js`;
sessionStorage.clear();

export default {
  name: "readPage",
  components: {
    FileText,
    Play,
    Pause,
    RotateCcw,
    Minus,
    Plus,
  },
  data() {
    return {
      wordsToRead: "",
      wordToShow: "",
      wordsArray: [],
      wordPerMin: 300,
      pause: true,
      currentWord: 0,
      fileName: "",
      hasText: false,
    };
  },
  methods: {
    textToArray(text) {
      text = text.replace(/  +/g, " ");
      text = text.replace(/\n/g, " ");
      this.wordsArray = text.split(" ").filter(w => w.trim() !== "");
    },
    sleep(milliseconds) {
      return new Promise((resolve) => setTimeout(resolve, milliseconds));
    },
    async playWordsLoop() {
      for (var i = this.currentWord; i < this.wordsArray.length; i++) {
        if (!this.pause) {
          this.wordToShow = this.wordsArray[i];
          this.currentWord = i;
          if (i === this.wordsArray.length - 1) {
            this.wordsToRead = "";
            this.pause = true;
          }
          await this.sleep((60 / this.wordPerMin) * 1000);
        } else {
          break;
        }
      }
    },
    async previewFiles(event) {
      var file = event.target.files[0];
      if (!file) return;
      this.fileName = file.name;
      this.hasText = false;

      const self = this;
      var fileReader = new FileReader();
      fileReader.onload = function () {
        var typedarray = new Uint8Array(this.result);
        const loadingTask = pdfjsLib.getDocument(typedarray);
        loadingTask.promise.then((pdf) => {
          let pageTexts = Array.from({ length: pdf.numPages }, async (v, i) => {
            return (await (await pdf.getPage(i + 1)).getTextContent()).items
              .map((token) => token.str)
              .join("");
          });

          return Promise.all(pageTexts).then((values) => {
            sessionStorage.clear();
            sessionStorage.setItem("text", values.join(" "));
            self.hasText = true;
          });
        });
      };
      fileReader.readAsArrayBuffer(file);
    },
    togglePlay() {
      if (!this.hasText || this.wordPerMin === 0) return;
      this.pause = !this.pause;
    },
    resetReading() {
      this.currentWord = 0;
      this.wordToShow = "";
      this.pause = true;
      this.wordsToRead = "";
      this.wordsArray = [];
      this.hasText = false;
    },
  },
  watch: {
    pause: function (val) {
      if (!val && this.wordPerMin > 0) {
        if (!this.wordsToRead) {
          this.wordsToRead = sessionStorage.getItem("text");
          sessionStorage.clear();
        }
        this.textToArray(this.wordsToRead);
        this.playWordsLoop();
      }
    },
  },
};
</script>
