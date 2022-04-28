<script >
export default {
  data() {
    return {
      trixText: '',
      loading: false,
    }
  },
  methods: {
    setTextToTrix() {
      this.trixText = document.getElementById("editor").value;
    },
    convert() {
      this.loading = true;
      setTimeout(() => {
        this.loading = false;
        this.$refs.converted.innerHTML = this.trixText.replace(/<em[^>]*>/g, '(').replace(/<\/em>/g, ')');
      }, "500")
    },
    trixStyle() {
      Trix.config.blockAttributes.default.tagName = "p"
      Trix.config.blockAttributes.default.breakOnReturn = true;

      Trix.Block.prototype.breaksOnReturn = function () {
        const attr = this.getLastAttribute();
        const config = Trix.getBlockConfig(attr ? attr : "default");
        return config ? config.breakOnReturn : false;
      };
      Trix.LineBreakInsertion.prototype.shouldInsertBlockBreak = function () {
        if (this.block.hasAttributes() && this.block.isListItem() && !this.block.isEmpty()) {
          return this.startLocation.offset > 0
        } else {
          return !this.shouldBreakFormattedBlock() ? this.breaksOnReturn : false;
        }
      };
    },
    trixChange() {
      document.addEventListener("trix-change", this.setTextToTrix);
    },
    copy(){
      document.execCommand('selectAll',false,null);
    },
    noImg(){
      document.addEventListener('trix-file-accept', function (e) {
        e.preventDefault();
      });
    }
  },
  computed: {
    loadingConvert() {
      return this.loading ? '<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" role="img" class="iconify iconify--eos-icons" width="20" height="20" preserveAspectRatio="xMidYMid meet" viewBox="0 0 24 24"><path fill="#b3b3b3" d="M12 2A10 10 0 1 0 22 12A10 10 0 0 0 12 2Zm0 18a8 8 0 1 1 8-8A8 8 0 0 1 12 20Z" opacity=".5"></path><path fill="currentColor" d="M20 12h2A10 10 0 0 0 12 2V4A8 8 0 0 1 20 12Z"><animateTransform attributeName="transform" dur="1s" from="0 12 12" repeatCount="indefinite" to="360 12 12" type="rotate"></animateTransform></path></svg>' : 'Convert'
    },
  },
  mounted() {
    this.noImg();
    this.trixStyle();
    this.trixChange();
  },
  beforeDestroy() {
    this.trixChange();
  }
}
</script>

<template>

  <main class="flex-1 w-full h-full mt-12 mb-0 relative">
    <section class="w-full h-auto relative">
      <div
        class="space-y-6 flex flex-col lg:mx-12 md:mx-12 mx-0 flex-wrap justify-center items-center relative break-words">
        <div class="flex-1 lg:w-[55rem] md:w-[40rem] sm:w-[35rem] w-[18rem]">
          <div class="text-white ">
            <input id="editor" type="hidden" name="content" v-model="trixText">
            <trix-editor input="editor" class="bg-gray-700 text-gray-200 leading-loose">

            </trix-editor>
            <div class="flex justify-end items-center mt-3">

              <button @click="convert" v-html="loadingConvert" type="button"
                class="bg-gray-600 px-5 py-1.5 rounded-lg font-semibold">

              </button>
            </div>
          </div>

        </div>
        <div class="flex-1 lg:w-[55rem] md:w-[40rem] sm:w-[35rem] w-[18rem]">
          <div class="text-center text-gray-300 font-bold mb-3 flex items-center justify-center">
            <span class="text-xl">
              Result
            </span>
          </div>

          <div class="text-white">
            <div contenteditable="true" ref="converted" 
              @cut.prevent="false" @paste.prevent="false"
              @keydown.prevent="(event) => event.metaKey ?  true : false"
              @click="copy"
              class="bg-gray-700 select-all cursor-default leading-loose text-gray-200 focus:outline-none rounded-lg py-1 px-4"
              style="min-height: 15em;">
            </div>
          </div>
        </div>
      </div>
    </section>
  </main>

</template>
