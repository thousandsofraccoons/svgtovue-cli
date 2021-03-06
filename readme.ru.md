# svgtovue-cli

![](https://img.shields.io/badge/WAI--ARIA-support-green.svg)

Сменить язык: [EN](https://github.com/thousandsofraccoons/svgtovue-cli/blob/master/README.md)

----
Компактная Node.JS библиотека, базирующаяся на SVGO, для конвертирования SVG файлов во Vue.JS шаблоны. + BaseIcon.vue компонент.

----
## About
- иконки будут добавлены в ваши компоненты автоматически (не нужно их импортировать вручную)
- работает с SSR и Nuxt
- ```v-html``` не используется
- 834 байт после Gzip сжатия
- автоматическая оптимизация SVG
- полностью настраиваемый шаблон
- viewbox будет добавлен автоматически
- поддержка WAI-ARIA


## Demo
[CodeSandBox](https://codesandbox.io/s/40xr5nv8w7)

```bash
# Глобальная установка
npm i svgtovue-cli -g
```

## Использование в командной строке
Просто выполните ```v-svg ./path/to/svg```. Ваши Vue шаблоны появятся в папке ```./path/to/templates/```.

<details>
    <summary>
        Нажмите здесь, чтобы увидеть пример ожидаемого результата работы библиотеки.
    </summary>

    <template>
        <g>
            <path d="M14.86 8.52A2.68 2.68 0 1 0 13 7.74a2.65 2.65 0 0 0 1.86.78zm-1-3.66a1.38 1.38 0 1 1 0 1.95 1.4 1.4 0 0 1-.39-1 1.44 1.44 0 0 1 .39-.95z"/>
            <path d="M18.42 0H1.58A1.58 1.58 0 0 0 0 1.58v16.84A1.58 1.58 0 0 0 1.58 20h16.84A1.58 1.58 0 0 0 20 18.42V1.58A1.58 1.58 0 0 0 18.42 0zm.32 18.44a.22.22 0 0 1-.22.22H1.58a.21.21 0 0 1-.22-.22V16H4.9a6.21 6.21 0 0 0 1.86-.32 6 6 0 0 0 1.68-.88l4.24-3.26a1.19 1.19 0 0 1 1.32 0l4.76 3.6zM7.66 13.8a4.47 4.47 0 0 1-1.32.68 4.82 4.82 0 0 1-1.46.26H1.26v-.22l.26-.24L6 10a1.19 1.19 0 0 1 1.58 0l2.22 2zM18.74 12v1.58l-4-3a2.34 2.34 0 0 0-1.42-.58 2.35 2.35 0 0 0-1.32.54l-1.12.86L8.4 9.18a2.43 2.43 0 0 0-3.3 0L1.26 12.8V1.58a.22.22 0 0 1 .22-.22h16.94a.22.22 0 0 1 .22.22z"/>
        </g>
    </template>

    <script>
    export default {
        data() {
            return {
                viewbox: "0 0 20 20"
            };
        },
        mounted() {
            this.$emit("onMounted", this.viewbox);
            }
        }
    </script>

</details>

### Использование во Vue

#### Простейший способ использовать полученные компоненты - использовать нижеследующий код. Просто скопируйте код и добавьте его глобально в main.js

##### ./components/BaseIcon.vue
<details>
    <summary>
        Нажмите здесь, чтобы развернуть. Просто создайте ./components/BaseIcon.vue со следующим содержимым:
    </summary>

    <template>
        <svg xmlns="http://www.w3.org/2000/svg"
        :height="height"
        :width="width"
        :viewBox="viewbox"
        :aria-labelledby="title"
        :aria-describedby="desc"
        :role="role"
        v-if="component">
            <title v-if="title">{{ title }}</title>
            <desc v-if="desc">{{desc}}</desc>

            <g :fill="color" :style="iconStyle" v-else>
                <component :is="component" @onMounted="getViewbox"></component>
            </g>

        </svg>
    </template>

    <script>
    export default {
        name: 'v-icon',
        data() {
            return {
                component: () =>
                    /* Укажите относительный путь к директории c шаблонами */
                    import('./templates/' + this.it + ".vue")
                    .then((template) => {
                        return template;
                    }),
                viewbox: '0 0 20 20'
            }
        },
        props: {
            it: {
                type: String,
                default: "default"
            },
            desc: {
                type: String,
            },
            role: {
                type: String,
                default: "img"
            },
            tabindex: {
                type: [Number, String],
                default: 0
            },
            title: {
                type: String,
                default: ""
            },
            iconStyle: {
                type: String,
                default: ""
            },
            width: {
                type: [Number, String],
                default: 24
            },
            height: {
                type: [Number, String],
                default: 24
            },
            color: {
                type: [String],
                default: "#333"
            }
        },
        methods: {
            getViewbox(viewbox) {
                this.viewbox = viewbox;
            }
        },
    };
    </script>

</details>

##### main.js

    import BaseIcon from './components/BaseIcon.vue'

    Vue.component("v-icon", BaseIcon);

##### Зачем копипаст?
Я не нашел ни одного приемлемого способа запаковать компонент как плагин, поскольку ```() => import()``` не поддерживает полностью динамический импорт. Сделовательно передать путь как опцию в него пока не получается. Буду очень признателен за любые советы в решении этой задачи.

## Использование <v-icon/>

Высотка и ширина иконки по-умолчанию: 24 x 24.

Значение параметра ``it`` должно быть идентично имени шаблона *.vue, который сгенерирован библиотекой.
```html
<v-icon it="arrow"/>
```

Конечно, такой способ записи также приемлем:
```html
<vIcon
    it="star"
    color="#42b983"
    height="200"
    width="200">
</vIcon>
```
