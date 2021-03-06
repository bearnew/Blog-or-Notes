## 1.webpack去掉无用CSS

### [purifycss-webpack](https://github.com/webpack-contrib/purifycss-webpack)
> 找了一圈可以通过构建来去除冗余css的插件，包括uncss下的gulp-uncss,purifycss下的gulp-purifycss以及purifycss-webpack。purifycss-webpack应该是矮子里面拔将军，最令人满意的了（虽然最后仍然没有选择它）。
#### 爬坑
##### 1.purifycss-webpack必须依赖extract-text-webpack-plugin才有效果
##### 2.extract-text-webpack-plugin不支持webpack@4.0+,需执行:
```js
npm install --save-dev extract-text-webpack-plugin@next 
```
#### 缺点
##### 1.只支持相对路径，导致通过alias配置的组件所需的css直接被移除了（导致alias配置的组件都需要手动加到paths里面）
##### 2.不支持css modules,通过设置purifyCssOptions配置whitelist，只会保留所有通过css modules手写的Css,并不会去除冗余css

## 2.stylelint规范
> 依赖的npm包
```js
npm i stylelint stylelint-config-standard stylelint-order stylelint-webpack-plugin --D
```
### 1.stylelint overview
#### stylelint的全部rule
* https://stylelint.io/user-guide/rules
#### 我们使用的rule
* 继承stylelint-config-standard, 规范css书写
  * 规则参考：https://github.com/stylelint/stylelint-config-standard/blob/master/index.js
* 使用style-order，规范css顺序
  * 规则参考：https://github.com/hudochenkov/stylelint-order
* 自定义rule
  
  ```js
    {
      "rules": {
        "indentation": 4, // 缩进
        "at-rule-no-unknown": null, // 是否禁止未知的规则(e.g., @include)
        "selector-pseudo-class-no-unknown": null, // 是否禁止未知的伪类(e.g., :global)
        "no-descending-specificity": null, // 是否禁止低权重的selctor在高权重的selector后面
        "order/order": [ // 定义css内容块的顺序
          "declarations", // CSS declarations (e. g., display: block)
          "custom-properties", // Custom properties (e. g., --property: 10px;)
          "dollar-variables", // Dollar variables (e. g., $variable)
          "rules", // Nested rules (e. g., a { span {} })
          "at-rules" // Nested at-rules (e. g., div { @media () {} })
        ],
        "order/properties-order": [] // css属性顺序配置
      }
    }
  ```
### 2.stylelint-webpack-plugin
```js
const StyleLintPlugin = require('stylelint-webpack-plugin');

plugins: [
    new StyleLintPlugin({
        configFile: '.stylelintrc.json', // 属性配置参考https://blog.csdn.net/sinat_31511831/article/details/54377837
        failOnError: false, // 是否开启css存在不规范，直接构建失败
        syntax: 'scss',
        cache: true, // 再次运行时只对改变的文件进行检查(提升stylelint效率)
        fix: false // 是否开启构建自动修复
    })
]
```
### 3.fixcss
在```package.json```中配置如下命令:
```
"fixcss": "stylelint src/components --fix",
```
### 4.stylelint-config-standard stylelint-order(排序)
```js
{
    "extends": "stylelint-config-standard",
    "plugins": ["stylelint-order"],
    "ignoreFiles": [
      "src/resource/css/*.css",
      "src/resource/css/modules/*.scss"
    ],
    "rules": {
      "indentation": 4,
      "no-duplicate-selectors": true,
      "at-rule-no-unknown": null,
      "selector-pseudo-class-no-unknown": null,
      "function-comma-space-after": null,
      "declaration-block-no-duplicate-properties": null,
      "no-descending-specificity": null,
      "order/order": [
        "declarations",
        "custom-properties",
        "dollar-variables",
        "rules",
        "at-rules"
      ],
      "order/properties-order": [
        "position",
        "top",
        "right",
        "bottom",
        "left",
        "float",
        "clear",
        "display",
        "flex",
        "flex-grow",
        "flex-shrink",
        "flex-basis",
        "flex-flow",
        "flex-direction",
        "flex-wrap",
        "justify-content",
        "align-content",
        "align-items",
        "align-self",
        "order",
        "grid",
        "grid-template-rows",
        "grid-template-columns",
        "grid-template-areas",
        "grid-auto-rows",
        "grid-auto-columns",
        "grid-auto-flow",
        "grid-column-gap",
        "grid-row-gap",
        "grid-template",
        "grid-template-rows",
        "grid-template-columns",
        "grid-template-areas",
        "grid-gap",
        "grid-row-gap",
        "grid-column-gap",
        "grid-area",
        "grid-row-start",
        "grid-row-end",
        "grid-column-start",
        "grid-column-end",
        "grid-column",
        "grid-column-start",
        "grid-column-end",
        "grid-row",
        "grid-row-start",
        "grid-row-end",
        "table-layout",
        "empty-cells",
        "caption-side",
        "border-collapse",
        "border-spacing",
        "list-style",
        "list-style-type",
        "list-style-position",
        "list-style-image",
        "ruby-align",
        "ruby-merge",
        "ruby-position",
        "box-sizing",
        "width",
        "min-width",
        "max-width",
        "height",
        "min-height",
        "max-height",
        "padding",
        "padding-top",
        "padding-right",
        "padding-bottom",
        "padding-left",
        "border",
        "border-width",
        "border-top-width",
        "border-right-width",
        "border-bottom-width",
        "border-left-width",
        "border-style",
        "border-top-style",
        "border-right-style",
        "border-bottom-style",
        "border-left-style",
        "border-color",
        "border-top-color",
        "border-right-color",
        "border-bottom-color",
        "border-left-color",
        "border-image",
        "border-image-source",
        "border-image-slice",
        "border-image-width",
        "border-image-outset",
        "border-image-repeat",
        "border-top",
        "border-top-width",
        "border-top-style",
        "border-top-color",
        "border-top",
        "border-right-width",
        "border-right-style",
        "border-right-color",
        "border-bottom",
        "border-bottom-width",
        "border-bottom-style",
        "border-bottom-color",
        "border-left",
        "border-left-width",
        "border-left-style",
        "border-left-color",
        "border-radius",
        "border-top-right-radius",
        "border-bottom-right-radius",
        "border-bottom-left-radius",
        "border-top-left-radius",
        "outline",
        "outline-width",
        "outline-color",
        "outline-style",
        "outline-offset",
        "margin",
        "margin-top",
        "margin-right",
        "margin-bottom",
        "margin-left",
        "color",
        "background",
        "background-image",
        "background-position",
        "background-size",
        "background-repeat",
        "background-origin",
        "background-clip",
        "background-attachment",
        "background-color",
        "background-blend-mode",
        "isolation",
        "clip-path",
        "mask",
        "mask-image",
        "mask-mode",
        "mask-position",
        "mask-size",
        "mask-repeat",
        "mask-origin",
        "mask-clip",
        "mask-composite",
        "mask-type",
        "filter",
        "box-shadow",
        "opacity",
        "visibility",
        "overflow",
        "overflow-x",
        "overflow-y",
        "vertical-align",
        "columns",
        "columns-width",
        "columns-count",
        "column-rule",
        "column-rule-width",
        "column-rule-style",
        "column-rule-color",
        "column-fill",
        "column-span",
        "column-gap",
        "orphans",
        "writing-mode",
        "text-combine-upright",
        "unicode-bidi",
        "text-orientation",
        "direction",
        "text-rendering",
        "font-feature-settings",
        "font-language-override",
        "font",
        "font-style",
        "font-variant",
        "font-weight",
        "font-stretch",
        "font-size",
        "font-family",
        "line-height",
        "text-overflow",
        "white-space",
        "overflow-wrap",
        "word-wrap",
        "word-break",
        "line-break",
        "hyphens",
        "text-align",
        "text-align-last",
        "text-justify",
        "font-synthesis",
        "font-size-adjust",
        "letter-spacing",
        "font-kerning",
        "word-spacing",
        "text-transform",
        "quotes",
        "tab-size",
        "text-indent",
        "text-emphasis",
        "text-emphasis-style",
        "text-emphasis-color",
        "text-emphasis-position",
        "text-decoration",
        "text-decoration-color",
        "text-decoration-style",
        "text-decoration-line",
        "text-underline-position",
        "text-shadow",
        "image-rendering",
        "image-orientation",
        "image-resolution",
        "shape-image-threshold",
        "shape-outside",
        "shape-margin",
        "transform-style",
        "transform",
        "transform-box",
        "transform-origin",
        "perspective",
        "perspective-origin",
        "backface-visibility",
        "transition",
        "transition-property",
        "transition-duration",
        "transition-timing-function",
        "transition-delay",
        "animation",
        "animation-name",
        "animation-duration",
        "animation-timing-function",
        "animation-delay",
        "animation-iteration-count",
        "animation-direction",
        "animation-fill-mode",
        "animation-play-state",
        "scroll-behavior",
        "scroll-snap-type",
        "scroll-snap-destination",
        "scroll-snap-coordinate",
        "resize",
        "cursor",
        "touch-action",
        "caret-color",
        "ime-mode",
        "object-fit",
        "object-position",
        "content",
        "counter-reset",
        "counter-increment",
        "will-change",
        "pointer-events",
        "z-index",
        "all",
        "page-break-before",
        "page-break-after",
        "page-break-inside",
        "widows"
      ]
    }
}
```