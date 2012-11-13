title: «Резиновость» в рамках разумного
link: http://paradigm.ru/max-width-for-ie
creator: admin
description: 
post_id: 137
post_date: 2007-12-28 01:10:08
post_date_gmt: 2007-12-27 22:10:08
comment_status: open
post_name: max-width-for-ie
status: publish
post_type: post

# «Резиновость» в рамках разумного

Как известно, для определения минимального и максимального значения размеров «резиновых» блоков в (X)HTML, предусмотрены свойства CSS `max-width` и `max-height`. Все браузеры уже года полтора правильно их интерпретируют. Кроме IE, разработчики которого по каким-то умным маркетинговым причинам клали болт на многие детали стандартов.

Тем не менее, возможность задавать предельные значения для масштабируемых блоков очень полезна. Например, для того чтобы «резиновая» верстка не теряла товарный вид при максимизации окна браузера на большом мониторе, заставляя пользователя читать полуметровые строки текста.

Раньше я решал этот вопрос с помощью JavaScript: небольшая функция автоматически корректировала ширину «резиновых» контейнеров при изменении размера окна браузера. Вчера был найден довольно более красивый способ заставить IE ограничивать масштабируемые блоки. Тоже на основе JS. 

В имлементации CSS IE есть своя-собственная конструкция  `expression`, чье значение может вычисляться с помощью client-side скрипта. Например, так:
    
    div {
       width: expression(750 + "px");
    }

Что эквивалентно:
    
    div {
       width: 750px;
    }

Используя expression в сочетании со стандартными свойствами CSS, можно добиться необходимого эффекта в отношении масштабируемых блоков:
    
    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
    
    <html>
    <head>
    <title>Limiting width</title>
    <style type="text/css">
    div#cont { 
    // min-width и max-width для IE
       width:expression(document.body.clientWidth > 776 ? 
          "777px" : (document.body.clientWidth < 334 ? "333px" : "auto"));
    // то же, для всех остальных
       max-width: 777px;
       min-width: 333px;
    
       height:320px;
       text-align:center;
       color:black;
       background:#f5f5f5;
    }
    </style>
    </head>
    
    <body>
    
    <div id="cont">Hello World!</div>
    
    </body>
    </html>

Файл лежит здесь: [http://things.paradigm.ru/limited-width.html](http://things.paradigm.ru/limited-width.html). Я проверил работоспособность этого кода в IE 5.55, 6, 7, Firefox и Opera. Теоретически, должно работать и в IE 5.01, т.к. expression появился именно в ней, но проверить не начем  эта версия на моем компьютере почему-то не уживается с пятью другими инсталяциями IE (:

Точно таким же образом, как и ширину, можно контролировать высоту блоков. Разница в том, что вместо параметра `document.body.clientWidth`, потребуется использовать `this.scrollHeight`, а изменять, вместо `width`, CSS свойство `height`.

Основано на [материалах](http://b23.ru/clx) perishablepress.com.

## Comments

**[SHAman](#143 "2007-12-28 17:11:10"):** Спасибо, полезная статья. Правда, использование expression, все же, не лучший выход... Все-таки, это почти хак.

**[admin](#146 "2007-12-28 23:06:22"):** SHAman: expression сделает CSS невалидным. В остальном, на мой взгляд, это вполне неплохое решение, т.к. оно работает в IE и не мешает работать нормальному отображению страницы в других браузерах.

**[Neutrino](#153 "2007-12-30 00:09:21"):** У меня есть [статья](http://www.webmakerslounge.com/css/ie6-min-max-width/) описывающая схожую проблему, а в комментариях есть несколько интересных решений.

**[admin](#156 "2007-12-30 00:39:48"):** Neutrino: Спасибо за ссылку.
