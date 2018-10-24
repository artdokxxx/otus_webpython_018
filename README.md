# BugReport (Webpack-сборка)

Допустим, имеется JS-пакет <a href="https://github.com/BorisPlus/BugReport" target="_blank">https://github.com/BorisPlus/BugReport</a> (мдя, почему не работает `<a href="..." target="_blank">...</a>` или `[link](url){:target="_blank"}`).

Задача в упаковке его с использованием Webpack.

## Описание процесса сборки модуля с использованием Webpack

### Предварительно

В терминале (возможно Вам понадобятся права _root_, прибегните к `sudo` или `su`):

```bash
apt-get install -y nodejs
nodejs -v
npm install --global webpack
npm install --global webpack-cli
```

### Переработка (портирование) проекта

Можно ничего не делать и запустить сборку исходного _bug_report.js_:
```html
module.exports = {
    entry: './src/bug_report',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'bug_report.bundle.js'
    },
    mode: 'production',
};
```

Понятно, что все заработает для <a href="https://github.com/BorisPlus/BugReport#базовый-вариант" target="_blank">базового примера</a>.

Но если необходимо привязать объект BugReport к дополнительной "форме", то:
* либо дописать соответствующи код в конец _bug_report.js_
    ```
    let example_bug_report = new BugReport(
        'example',
        'Патлумачце дадаткова і пакажыце кантактныя дадзеныя, калі хочаце.'
    );
    let my_bug_report = new BugReport(
        'my',
        'Leave a comment and contact to contact you if you want.'
    );
    ```
  и запустить выше указанную пересборку.
  
* либо исходный код (портировать) немного переработать.

Почему нужно переработать. Логично держать "ядро" библиотеки отдельно от места его использования\вызова. Нужно, значит, создать отдельный файл `my.bug_report.js` с указанными в прошлом пункте объявлениями объектов. А чтоб этот файл "увидел" файл "ядра", необходимо применить import и export.

Перво наперво что нужно сделать, так это объявить, что класс BugReport должен экспортироваться. Добавим прямо в конец `bug_report.js`:

```html
export {BugReport};
```

Теперь в `my.bug_report.js` импортируем:

```html
import { BugReport } from './bug_report';
```

Это все.

### Сборка проекта

При отладке мне нравится использовать режим отслеживания изменений и задержку на пересборку.

Для этого есть `--watch` ключ:

```bash
npx webpack --config webpack.config.js --watch
```

или если в `webpack.config.json`:

```
module.exports = {
    ...
    watch: true,
    watchOptions: {
        aggregateTimeout: 300
    }
    ...
};
```

Запуск сборки

```bash
cd project/
npm run build
```

или

```bash
cd project/
npx webpack --config webpack.config.js --watch
```

### Проверка работоспособности

Откройте проектный пример <a href="https://github.com/BorisPlus//otus_webpython_018/project/examples/examples.html" target="_blank">examples.html</a>.

Поведение идентично изначальному, см. GIF-анимацию:

<kbd><img src='README.files/img/animate/bug_report.gif' title='bug_report.gif'></kbd>

## Coderfriendly-подход

Если в конфиг `webpack.config.json` в позицию `output` добавить:

```html
module.exports = {
    ...
    output: {
        ...
        libraryTarget: 'var',
        library: 'PackedBugReport'
        ...
    },
    mode: 'production',
};
```

то сторонним разработчикам совсем не обязательно пересобирать проект и "зашивать" (как это было сделано с `my.bug_report.js`) свою реализацию в собираемый "модуль". 

Достаточно сделать в `my.bug_report.js` проброс экспорта:

```html
export { BugReport };
```

и стороннему разработчику станет возможным делать привязку к своим объектам _BugReport_ (подгружая класс _**PackedBugReport**.BugReport_ внутри HTML кода или же в отдельном своем файле):

```html
let module_usage_bug_report = new PackedBugReport.BugReport('module_usage');
```

Это продемонстрировано в "Примере №4" файла <a href="https://github.com/BorisPlus/otus_webpython_018/project/examples/examples.html" target="_blank">примеров</a>, где первые три примера подгружаются из webpack-сборки, а последний - с использованием стороннего (с точки зрения данной webpack-сборки) файла `module_usage.bug_report.js`.

GIF-анимация "Примера №4":
<kbd><img src='README.files/img/animate/packed_bug_report.gif' title='packed_bug_report.gif'></kbd>

## Вывод

Модульная инкапсуляция и разрешение на импорт только дозволенного - это конечно плюс, но требует опыта, и по отладке в том числе.

## Авторы

* **BorisPlus** - [https://github.com/BorisPlus/otus_webpython_018](https://github.com/BorisPlus/otus_webpython_018)

## Лицензия

Свободно

## Дополнительные сведения

Проект в рамках домашнего задания курса "Web-разработчик на Python" на https://otus.ru/learning
