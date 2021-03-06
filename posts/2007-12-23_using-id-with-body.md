title: Идентификация «тела»
link: http://paradigm.ru/using-id-with-body
creator: admin
description:
post_id: 118
created: 2007/12/23 01:48:18
created_gmt: 2007/12/22 22:48:18
comment_status: open
post_name: using-id-with-body
status: publish
post_type: post

# Идентификация «тела»

В статье рассматриваются примеры использования атрибута `id` тега `body` для оформления специфических элементов веб-страниц.

**Автор:** Chris Coyier, [css-tricks.com](http://css-tricks.com) **Оригинал:**[ http://css-tricks.com/id-your-body-for-greater-css-control-and-specificity/]( http://css-tricks.com/id-your-body-for-greater-css-control-and-specificity/) **Перевод:** Александр Мусаев, [paradigm.ru]()

Представим, такую ситуацию: по какой-то причине у нас возникла необходимость изменить цвет ссылок, но не на всем сайте, а только на одной странице с контактами. На других страницах их оформление по прежнему должно оставаться стандартным. Существует несколько способов добиться этого:

  * **Для страницы контактов можно определить отдельный CSS файл.** Это не идеальный вариант, влекущий за собой дополнительную работу. При необходимости внести любое изменение в таблицу стилей, понадобится каждый раз редактировать два отдельных файла.
  * **Для всех ссылок на странице контактов можно задать отдельный класс.** Способ так же не идеален, по причине своей избыточности и противоречия семантическому подходу (почему у всех ссылок на одной странице сайта класс определен, в то время как на всех остальных — нет, при том, что никакой логической разницы между ними не наблюдается?).
  * **Лучший вариант решения — задать уникальный идентификатор элементу `body` на нужной странице.** Это наиболее грамотное решение задачи, при котором можно использовать один единственный список стилей, ограничившись добавлением в него всего одного селектора.

### Реализация

Во-первых, зададим значение тага id для тега body нужной страницы:

       ...
    </head>

    <body id="contact-page">
       ...


Во-вторых, добавим новый селектор в CSS файл, определив в нем альтернативный цвет для всех ссылок внутри контейнера `body` с заданным идентификатором:

    a {
    color: blue;
    }

    body#contact-page a {
       color: red;
    }

Как еще можно использовать этот метод? Описанная техника может быть не менее полезной при оформлении навигационных элементов. Рассмотрим конкретный пример:

![](/media/tabbednav.jpg)

На скриншоте закладка «Forums» выделена как активная. По всей видимости это реализовано изменением в ее CSS оформлении (например, сдвигом фонового изображения). HTML код может выглядеть так:

    ...
    <li><a href="/fieldtips">Field tips</a></li>
    <li class="active"><a href="/forums">Forums</a></li>
    ...

В данном случае, нужное изменение в стиле задается в описании класса «active», который определен для соответствующего элемента списка. Но что произойдет, когда мы перейдем на другую страницу? Понадобится перенести определитель класса на соответствующий элемент списка, что не очень удобно, т.к. требует уникальности кода навигационного блока на каждой странице. А если понадобится добавить в список еще один элемент, это потребует еще большей работы. Так не годится.

Поступим иначе. Во-первых, для того чтобы не повторять код навигационного меню на каждой странице, запишем его в отдельный файл, после чего его можно будет включать с помощью нехитрого PHP кода:

    <?php include_once("nav.html"); ?>

Далее, вместо того, чтобы задавать класс только для активного элемента меню, определим уникальные классы для всех, после чего зададим идентификатор контейнеру `body`.

       ...
    </head>

    <body id="field-tips">
       ...
       <li class="fieldtips"><a href="/fieldtips">Field tips</a></li>
       <li class="forums"><a href="/forums">Forums</a></li>
       ...

Теперь мы имеем возможность определять специфику оформления активного элемента с помощью CSS:

    #field-tips li.fieldtips, #forums li.forums {
       background-position: bottom;
    }

Благодаря этому подходу мы добились того, что код навигационного блока будет одинаковым для каждой страницы, но отображение активного элемента будет переключаться в соответствии с открываемой в браузере страницей.

Еще немного облегчить себе жизнь можно автоматизировав идентификацию тега body:

    <body id="<?= basename($_SERVER['PHP_SELF'], ".php")?>">

Теперь `id` на каждой странице не придется прописывать вручную. Его значение будет соответствовать имени соответствующего файла (например, index для `index.php`).

Прим. переводчика: Я бы хотел добавить, что аналогичного результата можно добиться альтернативным способом — генерацией HTML кода меню на серверной стороне. При этом можно было бы добиться сокращения объема кода, избавив себя от необходимости в идентификаторах для всех элементов меню, кроме активного, и определению CSS класса для каждого из них. Код мог бы выглядеть так:

    <?php
