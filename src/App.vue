<script setup lang="ts">
import Header from './components/Header.vue'
</script>

<template>
    <Header />
    <main class="main container">
        <div class="row">
            <div class="col-left">
                <textarea
                    v-model="textareaValue"
                    @paste="processTextareaString"
                    placeholder="Insert your scss variables"
                ></textarea>
                <button class="save" type="button" @click="saveTextContent">
                    Save
                </button>
            </div>
            <div class="col-right">
                <div class="group">
                    <label>Search by property</label>
                    <input
                        type="text"
                        v-model="searchPropValue"
                        @input="searchByProperty"
                    />
                </div>

                <div class="group">
                    <label>Search by value</label>
                    <input
                        type="text"
                        v-model="searchCostValue"
                        @input="searchByValue"
                    />
                </div>
            </div>
        </div>
    </main>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
export default defineComponent({
    data() {
        return {
            textareaValue: '',
            searchPropValue: '',
            searchCostValue: '',
            lsKey: 'ssv.textarea-value',
            searchObject: {} as { [index: string]: any },
        }
    },
    methods: {
        saveTextContent() {
            console.log('Saving data...', this.textareaValue.length)

            if (this.textareaValue.length > 0) {
                localStorage.setItem(this.lsKey, this.textareaValue)
            }
        },
        getSavedContent() {
            this.textareaValue = localStorage.getItem(this.lsKey) || ''
        },
        searchByProperty() {
            this.searchCostValue = ''
            this.processTextareaString()
            if (this.searchPropValue in this.searchObject) {
                this.searchCostValue = this.searchObject[this.searchPropValue]
            }
        },
        searchByValue() {
            this.searchPropValue = ''
            this.processTextareaString()
            const entries = Object.entries(this.searchObject)
            const entry = entries.find(
                (entry) => entry[1] == this.searchCostValue
            )
            if (entry) {
                this.searchPropValue = entry[0]
            }
        },
        processTextareaString() {
            if (Object.keys(this.searchObject).length > 0) return

            if (this.textareaValue.length > 0) {
                const lines = this.textareaValue.split('\n')
                lines.forEach((line) => {
                    if (line.indexOf(':') > -1) {
                        const [prop, val] = line.split(':')
                        this.searchObject[prop.trim()] = val
                            .trim()
                            .replace(';', '')
                    }
                })
            }
        },
    },
    mounted() {
        this.getSavedContent()
    },
})
</script>
