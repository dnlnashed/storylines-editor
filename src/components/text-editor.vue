<template>
    <div class="flex flex-col mt-4">
        <label class="editor-label text-left">{{ $t('editor.slides.panel.title') }}:</label>
        <input class="editor-input" type="text" v-model="panel.title" />
        <label class="editor-label text-left mt-2">{{ $t('editor.slides.panel.body') }}:</label>
        <v-md-editor
            v-model="panel.content"
            height="400px"
            left-toolbar="undo redo clear | h bold italic strikethrough quote subsuper | ul ol table hr | addLink image code | save"
            :toolbar="toolbar"
        ></v-md-editor>
    </div>
</template>

<script lang="ts">
import { Prop, Vue } from 'vue-property-decorator';
import { TextPanel } from '@/definitions';

interface MDEditor {
    insert(callback: (selected: string) => { text: string; selected: string }): void;
}

export default class TextEditorV extends Vue {
    @Prop() panel!: TextPanel;
    @Prop({ default: false }) centerSlide!: boolean;
    @Prop({ default: false }) dynamicSelected!: boolean;

    toolbar = {
        subsuper: {
            title: 'Superscript/Subscript',
            text: 'T',
            menus: [
                {
                    name: 'Superscript',
                    text: 'Superscript',
                    action(editor: MDEditor): void {
                        editor.insert((selected: string) => {
                            const content = selected || ``;

                            return {
                                text: `<sup>${content}</sup>`,
                                selected: selected
                            };
                        });
                    }
                },
                {
                    name: 'Subscript',
                    text: 'Subscript',
                    action(editor: MDEditor): void {
                        editor.insert((selected: string) => {
                            const content = selected || ``;

                            return {
                                text: `<sub>${content}</sub>`,
                                selected: selected
                            };
                        });
                    }
                }
            ]
        },
        addLink: {
            title: 'Insert Link',
            icon: 'v-md-icon-link',
            menus: [
                {
                    name: 'Add External Link (New Tab)',
                    text: 'Add External Link (New Tab)',
                    action(editor: MDEditor): void {
                        editor.insert((selected: string) => {
                            const content = selected || ``;

                            return {
                                text: `[${content}](http://)`,
                                selected: selected
                            };
                        });
                    }
                },
                {
                    name: 'Add External Link (Same Tab)',
                    text: 'Add External Link (Same Tab)',
                    action(editor: MDEditor): void {
                        editor.insert((selected: string) => {
                            const content = selected || ``;

                            return {
                                text: `<a href='http://' target='_self'>${content}</a>`,
                                selected: selected
                            };
                        });
                    }
                },
                {
                    name: 'Add Dynamic Link',
                    text: 'Add Dynamic Link',
                    action(editor: MDEditor): void {
                        editor.insert((selected: string) => {
                            const content = selected || ``;

                            return {
                                text: `<a panel='panel-id-here'>${content}</a>`,
                                selected: selected
                            };
                        });
                    }
                }
            ]
        }
    };

    mounted(): void {
        if (this.centerSlide && this.dynamicSelected) {
            this.panel.customStyles += 'text-align: left !important;';
        } else if (!this.centerSlide && this.dynamicSelected) {
            this.panel.customStyles = (this.panel.customStyles || '').replace('text-align: left !important;', '');
        }
    }
}
</script>

<style lang="scss" scoped>
label {
    text-align: left !important;
}
</style>
