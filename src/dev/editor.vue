<template>
    <div class="container">
        <div id="editor">
            <mavon-editor style="height: 100%"
                          v-model="code"
                          :codeStyle="codeStyle"
                          :xssOptions="xssOptions"
                          :externalLink="external_link">
            </mavon-editor>
        </div>
        <div class="switch-code-style">
            <span>code style:</span>
            <select v-model="codeStyle">
                <option v-for="(val, key) in styles" :value="key">{{ key }}</option>
            </select>
        </div>
        <canvas id="canvas" width="1080" height="580"></canvas>
    </div>
</template>
<script>
    import styles from '../lib/core/hljs/lang.hljs.css.js'
    import rasterizeHTML from "rasterizeHTML"

    const code = '$y=x^2$';

    module.exports = {
        name: 'editor',
        data: function () {
            return {
                external_link: {
                    markdown_css: function() {
                        return '/markdown/github-markdown.min.css';
                    },
                    hljs_js: function() {
                        return '/highlightjs/highlight.min.js';
                    },
                    hljs_css: function(css) {
                        return '/highlightjs/styles/' + css + '.min.css';
                    },
                    hljs_lang: function(lang) {
                        return '/highlightjs/languages/' + lang + '.min.js';
                    },
                    katex_css: function() {
                        return '/katex/katex.min.css';
                    },
                    katex_js: function() {
                        return '/katex/katex.min.js';
                    }
                },
                codeStyle: "github",
                styles,
                code: '<span style="color:red;font-size:36px;">A</span> \n' + code + '\n',
                xssOptions:{
                    whiteList: {
                        span: ['style']
                    }
                }
            };
        },

        watch: {
            "code": {
                handler(newValue, oldValue) {
                    // ...
                    var input = document.getElementById("preview_switch"),
                        canvas = document.getElementById("canvas"),
                        oldText = input.innerHTML;

                    var draw = function () {
                        rasterizeHTML.drawHTML(input.innerHTML, canvas).then(function (result) {
                            console.log(result);
                        }, function (e) {
                            console.log('An error occured:', e);
                        });
                    };

                    input.onkeyup = function () {
                        if (input.value !== oldText) {
                            oldText = input.value;
                            canvas.getContext("2d").clearRect(0, 0, canvas.width, canvas.height);

                            draw();
                        }
                    };
                    draw();
                },
                deep: true
            }
        },
    }
</script>
<style>
    .container {
        margin: auto;
        width: 80%;
    }
    #editor {
        height: 580px;
    }
    .switch-code-style {
        margin-top: 10px;
        font-size: 16px;
    }
</style>
