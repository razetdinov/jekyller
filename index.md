---

layout: yandex2

style: |

---

# ![](themes/yandex2/images/logo-{{ site.presentation.lang }}.svg){:.logo}

## {{ site.presentation.title }}
{:.title}

### ![](themes/yandex2/images/title-logo-{{ site.presentation.lang }}.svg){{ site.presentation.service }}

{% if site.presentation.nda %}
![](themes/yandex2/images/title-nda.svg)
{:.nda}
{% endif %}

<div class="authors">
{% if site.author %}
<p>{{ site.author.name }}{% if site.author.position %}, {{ site.author.position }}{% endif %}</p>
{% endif %}

{% if site.author2 %}
<p>{{ site.author2.name }}{% if site.author2.position %}, {{ site.author2.position }}{% endif %}</p>
{% endif %}

</div>

## Я работаю в Яндексе 12 лет
{:.fullscreen}

![](pictures/bill.jpg)

## Зачем нужен монорепозиторий
{:.section}

## Монорепозиторий: плюсы
* Легкое реиспользование кода
* Легкое управление зависимостями
* Поддержка только актуальной версии
* Атомарные коммиты
* Масштабные рефакторинги
* Унификация всего

## Монорепозиторий: минусы
* Больше дискового пространства
* Дольше установка зависимостей
* Очередь на вливание
* Размытие ответственности

## Кто использует монорепозитории
* React
* Babel
* Jest
* Ember
* Meteor
* Angular

## Создание монорепозитория
{:.section}

## npx lerna init
```
packages/
package.json
lerna.json
```

**lerna.json**
```
{
  "version": "1.0.0"
  "packages": [
    "packages/*"
  ]
}
```

## Импорт проектов в монорепозиторий
{:.section}

## Импорт проектов в монорепозиторий
<table><tr><td>
Было:
<pre><code>
 
A/
  package.json
B/
  package.json
</code></pre>
</td><td>
Стало:
<pre><code>
packages/
  A/
    package.json
  B/
    package.json
</code></pre>
</td></tr></table>

## lerna import
* `lerna import <path-to-repository>`
* Собирает все коммиты
* Заменяет в них пути к файлам
* Добавляет их в монорепозиторий

## git subtree
* `git subtree add -P <prefix> <repository> <ref>`
* `git subtree pull -P <prefix> <repository> <ref>`
* `git subtree push -P <prefix> <repository> <ref>`
* Добавляет содержимое внешнего репозитория в монорепозиторий
* Позволяет обмениваться изменениями между ними

## Связывание зависимостей
{:.fullscreen}

![](pictures/deps.jpg)

<figure markdown="1">
Связывание зависимостей
</figure>


## lerna bootstrap
**packages/A/package.json**
```
  "dependencies": {
    "jest": "1.0.0"
  }
```

**packages/B/package.json**
```
  "dependencies": {
    "A": "1.0.0",
    "jest": "1.0.0"
  }
```

## lerna заменяет внутренние зависимости на символические ссылки
{:.blockquote}

## lerna bootstrap
```
packages/
  A/
    node_modules/
      jest/
  B/
    node_modules/
      A/ -> ../../A # символическая ссылка
      jest/
```

## Подъём зависимостей
{:.fullscreen}

![](pictures/lift.jpg)

<figure markdown="1">
Подъём зависимостей
</figure>

## Общие зависимости можно унести в корневой node_modules
{:.blockquote}

## lerna bootstrap ––hoist
```
node_modules/
  A/ -> ../packages/A # символическая ссылка
  jest/

packages/
  A/
    node_modules/
  B/
    node_modules/
```

## С помощью nohoist можно оставить зависимости на уровне пакета
{:.blockquote}

## lerna bootstrap ––hoist ––nohoist=jest
```
node_modules/
  A/ -> ../packages/A # символическая ссылка

packages/
  A/
    node_modules/
      jest/
  B/
    node_modules/
      jest/
```

## При подъёме зависимостей остаётся единственный package-lock.json в корне
{:.blockquote}

## yarn workspaces
{:.section}

## yarn из коробки умеет связывать и поднимать зависимости, оставляя единственный yarn.lock в корне
{:.blockquote}

## yarn workspaces
**package.json**
```json
{
  "private": true,
  "workspaces": {
    "packages": [
      "packages/*"
    ]
    "nohoist": [
      "**/jest"
    ]
  }
}
```

## yarn workspaces
```
node_modules/
  A/ -> ../packages/A # символическая ссылка

packages/
  A/
    node_modules/
      jest/
  B/
    node_modules/
      jest/
```

## yarn workspaces + lerna
lerna умеет работать с yarn workspaces.

<br/>

**lerna.json**
```json
{
  "npmClient": "yarn",
  "useWorkspaces": true
}
```

## А как же npm?
npm обещают базовую поддержку workspaces в седьмой версии и расширенную — в восьмой:

**npm v7 will have at least the Workspace feature support of Yarn, and will set the stage for more advanced workspace features in v8.**

**At minimum, you’ll be able to keep multiple related packages all together in a single repository, and test changes in an integrated environment, without continually re-linking.**

https://clck.ru/J5nrx

## Ждём
{:.fullscreen}

![](pictures/wait.jpg)

## Массовые операции
{:.fullscreen}

![](pictures/dogs.jpg)

<figure markdown="1">
Массовые операции
</figure>

## yarn и lerna позволяют выполнять операции сразу над несколькими пакетами
{:.blockquote}

## yarn workspace
* `yarn workspace <workspace> <command> [args]`
* `yarn workspace packages/A add jest`
* `yarn workspace packages/A test`
* `yarn workspace packages/A eslint .`

## yarn workspaces run
* `yarn workspaces run <command>`
* `yarn workspaces run add jest`
* `yarn workspaces run test`
* `yarn workspaces run eslint .`

## yarn workspace(s)
Плюсы:
* Запуск команд yarn
* Запуск npm-скриптов
* Запуск исполняемых файлов из `.bin`

Минусы:
* Невозможно запускать shell-команды
* Невозможно указать список пакетов
* Падает при первой же ошибке

## lerna run
* `lerna run <script> -- [args]`
* `lerna run test`
* `lerna run test --scope packages/A`
* `lerna run test --scope packages/A --scope packages/B`
* `lerna run test --parallel`
* `lerna run test --no-bail`

## lerna exec
* `lerna exec -- <command> [args]`
* `lerna exec -- rm -rf node_modules`
* `lerna exec -- eslint .`
* `lerna exec --scope packages/A -- eslint .`
* `lerna exec --scope packages/A --scope packages/B -- eslint .`
* `lerna exec --parallel -- eslint .`
* `lerna exec --no-bail -- eslint .`

## lerna run/exec
Плюсы:
* Запуск npm-скриптов
* Запуск исполняемых файлов из `.bin`
* Запуск произвольных команд
* Возможность указать список пакетов
* Возможность параллельного выполнения
* Возможность игнорировать ошибки

## Селективные операции
{:.fullscreen}

![](pictures/pit.jpg)

<figure markdown="1">
Селективные операции
</figure>

## lint-staged позволяет тестировать только изменённые файлы
{:.blockquote}

## lint-staged
**pakage.json**
```
{
  "lint-staged": {
    "*.js": "eslint"
  },
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  }
}
```

## jest умеет находить тесты только на изменившиеся файлы
{:.blockquote}

## jest ––findRelatedTests
**pakage.json**
```
{
  "lint-staged": {
    "src/**/*.js": "jest --findRelatedTests"
  },
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  }
}
```

## lerna умеет выполнять команды только для изменившихся пакетов
{:.blockquote}

## lerna run/exec ––since
* `lerna run --since <ref> <script> -- [args]`
* `lerna run --since master test`
* `lerna exec --since <ref> -- <command> [args]`
* `lerna exec --since master -- make test`

## Ссылки
* https://lerna.js.org/
* https://yarnpkg.com/en/docs/workspaces/
* https://trunkbaseddevelopment.com/monorepos/

## Спасибо
{:.icons}

![](pictures/mishanga.jpg)
*Мише Трошеву*

![](pictures/fresk.jpg)
*Гоше Беседину*

## Вопросы?
{:.contacts}

{% if site.author %}

<figure markdown="1">

### {{ site.author.name }}

{% if site.author.position %}
{{ site.author.position }}
{% endif %}

</figure>

{% endif %}

{% if site.author2 %}

<figure markdown="1">

### {{ site.author2.name }}

{% if site.author2.position %}
{{ site.author2.position }}
{% endif %}

</figure>

{% endif %}

<!-- разделитель контактов -->
-------

<!-- left -->
- {:.mail}azat@yandex-team.ru

<!-- right -->
- {:.telegram}razetdinov
