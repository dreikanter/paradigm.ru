title: JavaScript фреймворки теперь можно хостить на Google
link: http://paradigm.ru/google-ajax-libraries-ap
creator: admin
description: 
post_id: 350
created: 2008/05/28 16:03:36
created_gmt: 2008/05/28 13:03:36
comment_status: open
post_name: google-ajax-libraries-ap
status: publish
post_type: post

# JavaScript фреймворки теперь можно хостить на Google

В блоге [AJAX Search API](http://googleajaxsearchapi.blogspot.com/) вчера был проанонсирован [AJAX Libraries API](http://code.google.com/apis/ajaxlibs/). Суть состоит в том, что Google теперь позволяет всем желающим использовать JavaScript фреймворки, размещенные на их серверах. Сам API представляет собой простой в использовании механизм для подключения к НТМL-документам наиболее популярных JS-библиотек, среди которых jQuery, prototype, script.aculo.us, MooTools и dojo. Список, по всей видимости, будет расширяться.

Подключить необходимую библиотеку можно двумя способами. Первый  тривиальный, с помощью HTML-элемента script.
    
    <script src="http://ajax.googleapis.com/
        ajax/libs/prototype/1.6.0.2/prototype.js"></script>

  
  


Как не сложно догадаться, вместо `prototype/1.6.0.2` можно указать название любого другого фреймворка и его версии.

Второй способ более гибок. С помощью специального JS-метода `google.load()` можно задавать версию библиотеки не точно, а по первым цифрам.
    
    <script src="http://www.google.com/jsapi"></script>
    <script>
    google.load("prototype", "1.6");
    // 
    </script>

Заданное в примере значение «1.6» означает то, что будет автоматически выбрана самая свежая версия prototype в ветке 1.6. Таким образом, AJAX Libraries API может обеспечить автообновление JS-библиотек без каких-либо изменений в вызывающем скрипте (естветсственно, такое обновление ограничивается пределами нужной ветки).

К основным плюсам использования AL API можно отнести следующие:

  * Хранение скриптов на Google в некоторой степени снижает нагрузку на собственный веб-сервер.
  * Скорость загрузки страниц увеличивается, благодаря [переносу части запросов на другой хост](/2008/05/11/circumventing-browser-connection-limits/) и поддержке gz-сжатия.
  * При массовом использовании централизованно-хранимых фреймворков, кэш браузеров используется эффективнее. Файл, загруженный для одного веб-приложения, может быть повторно использован при работе с другим, использующим тот же фреймворк. Пока это лишь потенциальный бенефит сервиса, но, тем не менее, приятный.
  * Кроме того, благодаря Google можно облегчать дистрибутивы веб-приложений, использующих JS-фреймворки.

### Ссылки по теме

  * [Официальный анонс](http://googleajaxsearchapi.blogspot.com/) AJAX Search API.
  * [Подробное описание](http://code.google.com/apis/ajax/documentation/) метода google.load().
  * [Developers Guide](http://code.google.com/apis/ajaxlibs/documentation/), содержащий список поддерживаемых фреймворков с номерами версий.
  * [Announcing AJAX Libraries API](http://ajaxian.com/archives/announcing-ajax-libraries-api-speed-up-your-ajax-apps-with-googles-infrastructure): Speed up your Ajax apps with Google's infrastructure @ [ajaxian.com](http://ajaxian.com/)

## Comments

**[der Igel](#821 "2008/05/28 16:50:51"):** При прямом включении скрипта тоже можно указывать не полную версию, например http://ajax.googleapis.com/ajax/libs/jquery/1.2/jquery.js вернет последнюю версию jQUery из линейки 1.2.

**[admin](#822 "2008/05/28 17:15:23"):** der Igel: Интересно. В анонсе про это ничего не сказали. Недокументировананя фича? (:

**[reflexing](#1166 "2008/06/27 11:51:35"):** Скоро вообще ничего не надо будет делать - Гугл сделает всё за нас :) Вообще, идея - отличная. Буду пользоваться.

