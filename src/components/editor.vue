<template>
    <div class="editor-container">
        <div class="editor-header sticky flex items-center border-b border-black bg-gray-200 py-2 px-2 z-10">
            <span class="mx-1">
                <router-link
                    :to="{ name: 'home' }"
                    class="mt-1 flex justify-center h-full w-full"
                    v-tippy="{
                        delay: '200',
                        placement: 'right',
                        content: $t('editor.returnToLanding'),
                        animateFill: true
                    }"
                    target
                >
                    <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18.001" viewBox="0 0 18 18.001">
                        <path
                            id="logout-Icon-SVG-098767893"
                            d="M5.808,13.782v1.406A2.816,2.816,0,0,0,8.621,18h7.067A2.816,2.816,0,0,0,18.5,15.188V2.813A2.816,2.816,0,0,0,15.687,0H8.621A2.816,2.816,0,0,0,5.808,2.813V4.219a.7.7,0,0,0,1.406,0V2.813A1.408,1.408,0,0,1,8.621,1.406h7.067a1.408,1.408,0,0,1,1.406,1.406V15.188a1.408,1.408,0,0,1-1.406,1.406H8.621a1.408,1.408,0,0,1-1.406-1.406V13.782a.7.7,0,0,0-1.406,0ZM1.014,7.793,2.589,6.218a.7.7,0,0,1,.994.994l-1.12,1.12h8.443a.7.7,0,1,1,0,1.406H2.463l1.12,1.12a.7.7,0,1,1-.994.994L1.014,10.279A1.76,1.76,0,0,1,1.014,7.793Zm0,0"
                            transform="translate(-0.5)"
                        />
                    </svg>
                </router-link>
            </span>
            <div class="ml-3 flex flex-col">
                <span class="font-semibold text-lg">{{ metadata.title }}</span>
                <span :class="metadata.title ? 'text-xs' : ''">UUID: {{ uuid }}</span>
            </div>
            <span class="ml-auto"></span>
            <button
                v-if="unsavedChanges"
                @click="$vfm.open(`reload-config`)"
                class="editor-button border-2 border-red-700 text-red-700 rounded p-1 mr-2"
                v-tippy="{
                    delay: '200',
                    placement: 'bottom',
                    content: $t('editor.resetChanges'),
                    animateFill: true
                }"
            >
                <svg class="inline" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="18px" height="18px">
                    <path
                        d="M 2 2 L 4.9394531 4.9394531 C 3.1262684 6.7482143 2 9.2427079 2 12 C 2 17.514 6.486 22 12 22 C 17.514 22 22 17.514 22 12 C 22 6.486 17.514 2 12 2 L 12 4 C 16.411 4 20 7.589 20 12 C 20 16.411 16.411 20 12 20 C 7.589 20 4 16.411 4 12 C 4 9.7940092 4.9004767 7.7972757 6.3496094 6.3496094 L 9 9 L 9 2 L 2 2 z"
                    />
                </svg>
                <span class="font-normal ml-1">{{ $t('editor.resetChanges') }}</span>
            </button>
            <transition name="fade">
                <span v-if="unsavedChanges" class="border-2 border-red-700 text-red-700 rounded p-1 mr-2">
                    <span class="align-middle inline-block mr-1 pb-1 fill-current"
                        ><svg
                            clip-rule="evenodd"
                            fill-rule="evenodd"
                            class="fill-red-600"
                            width="18"
                            height="18"
                            stroke-linejoin="round"
                            stroke-miterlimit="2"
                            viewBox="0 0 24 24"
                            xmlns="http://www.w3.org/2000/svg"
                        >
                            <path
                                d="m12.002 21.534c5.518 0 9.998-4.48 9.998-9.998s-4.48-9.997-9.998-9.997c-5.517 0-9.997 4.479-9.997 9.997s4.48 9.998 9.997 9.998zm0-1.5c-4.69 0-8.497-3.808-8.497-8.498s3.807-8.497 8.497-8.497 8.498 3.807 8.498 8.497-3.808 8.498-8.498 8.498zm0-6.5c-.414 0-.75-.336-.75-.75v-5.5c0-.414.336-.75.75-.75s.75.336.75.75v5.5c0 .414-.336.75-.75.75zm-.002 3c.552 0 1-.448 1-1s-.448-1-1-1-1 .448-1 1 .448 1 1 1z"
                                fill-rule="nonzero"
                            />
                        </svg>
                    </span>
                    <span class="align-center inline-block select-none">{{ $t('editor.unsavedChanges') }}</span>
                </span>
            </transition>
            <slot name="langModal" v-bind="{ unsavedChanges: unsavedChanges }"></slot>
            <button @click="preview" class="editor-button bg-white border border-black hover:bg-gray-100">
                {{ $t('editor.preview') }}
            </button>
            <button @click="saveChanges" class="editor-button bg-black text-white hover:bg-gray-900" :disabled="saving">
                <span class="inline-block">{{ saving ? $t('editor.savingChanges') : $t('editor.saveChanges') }}</span>
                <span v-if="saving" class="align-middle inline-block px-1">
                    <spinner size="16px" color="#009cd1" class="ml-1 mb-1"></spinner>
                </span>
            </button>
            <button
                @click="$vfm.open(`help-panel`)"
                class="bg-white border border-black rounded-full w-9 h-9 hover:bg-gray-100"
                v-tippy="{
                    delay: '200',
                    placement: 'top',
                    content: $t('help.title'),
                    animateFill: true
                }"
            >
                <span class="bottom-0 question-mark-button"> ? </span>
            </button>
        </div>
        <div class="flex">
            <div class="w-80 flex-shrink-0 border-r border-black editor-toc">
                <div class="flex items-center justify-center border-b p-2">
                    <button class="editor-toc-button editor-button" @click.stop="$vfm.open('metadata-edit-modal')">
                        <span class="align-middle inline-block px-1"
                            ><svg
                                clip-rule="evenodd"
                                fill-rule="evenodd"
                                width="16"
                                height="16"
                                stroke-linejoin="round"
                                stroke-miterlimit="2"
                                viewBox="0 0 24 24"
                                xmlns="http://www.w3.org/2000/svg"
                            >
                                <path
                                    d="m4.481 15.659c-1.334 3.916-1.48 4.232-1.48 4.587 0 .528.46.749.749.749.352 0 .668-.137 4.574-1.492zm1.06-1.061 3.846 3.846 11.321-11.311c.195-.195.293-.45.293-.707 0-.255-.098-.51-.293-.706-.692-.691-1.742-1.74-2.435-2.432-.195-.195-.451-.293-.707-.293-.254 0-.51.098-.706.293z"
                                    fill-rule="nonzero"
                                />
                            </svg>
                        </span>
                        <span class="align-middle inline-block">{{ $t('editor.editMetadata') }}</span>
                    </button>
                </div>
                <slide-toc
                    :slides="slides"
                    :currentSlide="currentSlide"
                    :slideIndex="slideIndex"
                    @slide-change="selectSlide"
                    @slides-updated="updateSlides"
                    :configFileStructure="configFileStructure"
                    :lang="configLang"
                    :sourceCounts="sourceCounts"
                ></slide-toc>
            </div>
            <slide-editor
                ref="slide"
                :configFileStructure="configFileStructure"
                :currentSlide="currentSlide"
                :lang="configLang"
                :slideIndex="slideIndex"
                :isLast="slideIndex === slides.length - 1"
                :uid="uuid"
                @slide-change="selectSlide"
                @slide-edit="onSlidesEdited"
                @custom-slide-updated="updateCustomSlide"
                :sourceCounts="sourceCounts"
            ></slide-editor>
        </div>
        <div class="footer text-right pr-5 editor-button">
            <a
                :href="`mailto:applicationsdecartographieweb-webmappingapplications@ec.gc.ca?subject=${$t(
                    'editor.feedback.subject'
                )}`"
            >
                {{ $t('editor.feedback') }}
            </a>
        </div>
        <slot name="metadataModal"></slot>
        <help-panel :helpSections="helpSections" :originalTextArray="originalTextArray"></help-panel>
        <confirmation-modal
            :name="`reload-config`"
            :message="$t('editor.refreshChanges.modal')"
            @ok="$emit('refresh-config')"
        />
    </div>
</template>

<script lang="ts">
import { Options, Prop, Vue, Watch } from 'vue-property-decorator';
import { ConfigFileStructure, HelpSection, MetadataContent, Slide, SourceCounts, StoryRampConfig } from '@/definitions';
import { VueSpinnerOval } from 'vue3-spinners';
import axios from 'axios';
import { marked } from 'marked';

import SlideEditorV from './slide-editor.vue';
import SlideTocV from './slide-toc.vue';
import MetadataContentV from './helpers/metadata-content.vue';
import ConfirmationModalV from './helpers/confirmation-modal.vue';
import HelpPanelV from './help-panel.vue';
import HelpSectionV from './helpers/help-section.vue';

@Options({
    components: {
        'metadata-content': MetadataContentV,
        'confirmation-modal': ConfirmationModalV,
        spinner: VueSpinnerOval,
        'slide-editor': SlideEditorV,
        'slide-toc': SlideTocV,
        'help-panel': HelpPanelV,
        'help-section': HelpSectionV
    }
})
export default class EditorV extends Vue {
    @Prop() configs!: {
        [key: string]: StoryRampConfig | undefined;
    };
    @Prop() configFileStructure!: ConfigFileStructure | undefined;
    @Prop() sourceCounts!: SourceCounts;
    @Prop() metadata!: MetadataContent;
    @Prop() slides!: Slide[];
    @Prop() configLang!: string;
    @Prop() saving!: boolean;
    @Prop() unsavedChanges!: boolean;

    // Form properties.
    uuid = '';
    logoImage: undefined | File = undefined;
    loadSlides: undefined | Slide[] = undefined;
    currentSlide: Slide | string = '';
    slideIndex = -1;
    helpSections: HelpSection[] = [];
    helpMd = '';
    originalTextArray: string[] = [];

    @Watch('slides', { deep: true })
    onSlidesEdited(): void {
        this.$emit('save-status', true);
    }

    @Watch('metadata', { deep: true })
    onMetadataEdited(): void {
        this.$emit('save-status', true);
    }

    created(): void {
        this.loadSlides = this.slides;
        this.uuid = this.$route.params.uid as string;

        window.addEventListener('beforeunload', this.beforeWindowUnload);

        this.fetchMarkdown();
    }

    mounted(): void {
        // from https://css-tricks.com/how-to-detect-when-a-sticky-element-gets-pinned/
        const observer = new IntersectionObserver(([e]) => e.target.classList.toggle('z-40', e.intersectionRatio < 1), {
            threshold: [1]
        });

        observer.observe(document.querySelector('.editor-header') as Element);
    }

    beforeDestroy(): void {
        window.removeEventListener('beforeunload', this.beforeWindowUnload);
    }

    /**
     * Change current slide to selected slide.
     */
    selectSlide(index: number): void {
        // save changes to current slide before changing slides
        if (this.$refs.slide !== undefined) {
            (this.$refs.slide as SlideEditorV).saveChanges();
        }

        // Quickly swap to loading page, and then swap to new slide. Allows Vue to re-draw page correctly.
        this.currentSlide = {
            title: '',
            panel: [{ type: 'loading-page' }, { type: 'loading-page' }]
        };

        setTimeout(() => {
            this.currentSlide = index === -1 ? '' : (this.loadSlides as Slide[])[index];
            this.slideIndex = index;
            (this.$refs.slide as SlideEditorV).panelIndex = 0;
            (this.$refs.slide as SlideEditorV).advancedEditorView = false;
            window.scrollTo(0, 0);
        }, 5);
    }

    /**
     * Update slide for a custom config made through advanced editor.
     */
    updateCustomSlide(slideConfig: Slide, save?: boolean): void {
        this.currentSlide = slideConfig;
        this.slides[this.slideIndex] = slideConfig;
        // save changes emitted from advanced editor
        if (save) {
            this.$emit('save-changes');
        }
    }

    /**
     * Updates slides after adding, removing, or reordering.
     */
    updateSlides(slides: Slide[]): void {
        this.loadSlides = slides;
        this.slideIndex = this.loadSlides.indexOf(this.currentSlide as Slide);
    }

    /**
     * Fetch markdown content for help panel.
     */
    fetchMarkdown(): void {
        const helpPath = process.env.NODE_ENV === 'development' ? `../../help/` : `./help/`;
        const helpFile = `respect-help-${this.$route.params.lang}.md`;

        axios.get(`${helpPath}${helpFile}`).then((r) => {
            const reg = /^#\s(.*)\n{2}(?:.+|\n(?!\n{2,}))*/gm;
            const renderer = new marked.Renderer();
            renderer.image = (href: string, title: string, text: string) => {
                if (href.indexOf('http') === -1) {
                    href = helpPath + 'images/' + href;
                }
                return `<img src="${href}" alt="${text}">`;
            };
            this.helpMd = r.data.replace(new RegExp(String.fromCharCode(13), 'g'), '');
            let section;
            while ((section = reg.exec(this.helpMd))) {
                const info_results = marked(section[0].split('\n').splice(2).join('\n'), { renderer }) as string;
                this.helpSections.push({
                    header: section[1],
                    info: info_results,
                    drawn: true,
                    expanded: false
                });
                //copy of the original text to refer to after highlighting
                this.originalTextArray.push(info_results);
            }
        });
    }

    /**
     * Open current editor config as a new Storylines product in new tab.
     */
    preview(): void {
        // save current slide final changes before previewing product
        if (this.$refs.slide != null && this.currentSlide !== '') {
            (this.$refs.slide as SlideEditorV).saveChanges();
        }

        setTimeout(() => {
            const routeData = this.$router.resolve({
                name: 'preview',
                params: { lang: this.configLang, uid: this.uuid }
            });
            const previewTab = window.open(routeData.href, '_blank');
            (previewTab as Window).props = {
                configs: this.configs,
                configFileStructure: this.configFileStructure
            };
        }, 5);
    }

    saveChanges(): void {
        // save current slide final changes before generating config file
        if (this.$refs.slide !== undefined) {
            (this.$refs.slide as SlideEditorV).saveChanges();
        }

        // emit save changes event
        this.$emit('save-changes');
    }

    beforeWindowUnload(e: BeforeUnloadEvent): void {
        // show popup if when leaving page with unsaved changes
        if (this.unsavedChanges && !window.confirm()) {
            e.preventDefault();
        }
    }
}
</script>

<style lang="scss">
.editor-container {
    margin: 0 auto;
}

.editor-label {
    width: 10vw;
    text-align: right;
    margin-right: 15px;
    display: inline-block;
}

.editor-h3 {
    font-size: larger;
}

.editor-input {
    padding: 5px 10px;
    margin-top: 5px;
    border: 1px solid black;
    display: inline;
}

.editor-container .input-error {
    border: 1px solid red;
}

.editor-button {
    padding: 5px 12px;
    margin: 0px 10px;
    font-weight: 600;
    transition-duration: 0.2s;
}

.editor-button:hover:enabled {
    background-color: #dbdbdb;
    color: black;
}

.editor-button:disabled {
    border: 1px solid gray;
    color: gray;
    cursor: not-allowed;
}

.editor-toc-button {
    background-color: #f3f4f6;
    color: black;
    border: none;
    transition-duration: 0.2s;
    padding: 0.25 0.25em !important;
}

.editor-header {
    top: -1px;
    padding-top: 9px;
}

.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.2s;
}

.fade-enter,
.fade-leave-to {
    opacity: 0;
}

.question-mark-button {
    font-size: 24px;
    line-height: 1.8rem;
}
</style>
