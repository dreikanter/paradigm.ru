title: WP Flipper  стрелочная навигация для блога
link: http://paradigm.ru/wp-flipper-announce
creator: admin
description: 
post_id: 343
post_date: 2008-05-23 12:00:19
post_date_gmt: 2008-05-23 09:00:19
comment_status: open
post_name: wp-flipper-announce
status: publish
post_type: post

# WP Flipper  стрелочная навигация для блога

Теперь страницы Paradigm.ru можно листать с помощью Ctrl-left и Ctrl-right. Идея написать такой плагин для Wordpress витала в воздухе уже давно и, в итоге, материализовалась. По сравнению с [предыдущим](/wp-postlist/), плагин получился довольно простой, не требующий никаких настроек в контрольной панели. Достаточно включить и все заработает. Ссылка на архив:

[http://things.paradigm.ru/wp-flipper.zip](http://things.paradigm.ru/wp-flipper.zip)

Более подробное описание находится на **[странице плагина](/wp-flipper/)**, а пока вкратце расскажу о технической стороне вопроса для тех, кому интересно.

В принципе, сам процесс «отлавливания» нажатий на горячие клавиши довольно прост. Уже давно в [Техногрете](http://www.artlebedev.ru/tools/technogrette/) на эту тему была опубликована статья с примером. Код на JavaScript выглядит следующим образом:
    
    function NavigateThrough (event)
    {
      switch (event.keyCode ? event.keyCode : event.which ? event.which : null)
      {
        case 0x25:
          link = document.getElementById ('PrevLink');
          break;
        case 0x27:
          link = document.getElementById ('NextLink');
          break;
      }
      if (link && link.href) document.location = link.href;  
    }

Для того, чтобы функция срабатывала, необходимо зарегистрировать обработчик на событие `document.onkeydown` и прописать соответствующие id у навигационных ссылок (тех, которым соответствует «лево» и «право»). Либо просто вставить внутрь кода URL правой и левой страниц, как я и сделал.

При наличии возможности динамически генерировать JavaScript на серверной стороне, так же может быть полезно убирать из блока switch первый или второй case, когда в них нет необходимости, т. к. на крайних страницах переход может быть только в одну из двух сторон. В плагине все происходит именно так, и лишний обработчик автоматически убирается.

JS-листалка работает в Firefox и, после небольшой модификации, в IE7*.

* Установленный у меня IE8b (работающий в режиме IE7) говорит, что объект event не существует и прекращает исполнение скрипта при первом обращении к функции. Чтобы все заработало, необходимо добавить в функцию первую строку: `if(!event) event = window.event;`.

Что же касется Оперы, процитирую статью Андрея Шитова:

> Опера — исключение. Этот браузер передает управление обработчику события после того, как выполнит действие по умолчанию (комбинация Ctrl + стрелка в ней уже занята). Поэтому в Опере приходится нажимать не только Ctrl, но и Shift — такая трехклавишная комбинация соответствует навигации по ссылкам из тегов.

Я проверял код в Opera 9.50 build 9945, все обстоит именно так. Кстати, помимо приведенного выше, я пробовал использовать другой очень похожий [вариант скрипта](http://b23.ru/ynj). По непонятной причине он обрушивал Оперу насмерть. Это явный баг и в релизе, вероятно, он будет исправлен.

Для того, чтобы не плодить [лишние соединения с сервером](/2008/05/11/circumventing-browser-connection-limits/), плагин вставляет JavaScript прямо в код страницы (он совсем небольшой, поэтому такой метод подключения предпочтителен). Вставка происходит по хуку wp_footer, так что если вдруг код на странице не появляется, причина, скорее всего, в том, что в вашей теме нет вызова `wp_footer()`. Такое иногда бывает, т. к. сам по себе этот вызов не критичен. На стандартной теме Кубрик все работает, проверено.

**Важный PS:** некоторое время назад я зарегистрировался в Plugin Directory на wordpress.org, с целью добавить туда [другой свой плагин](/wp-postlist/). По неясной причине, SVN говорит об ошибке 403 и не хочет всасывать файлы. Пробовал через консольный клиент и Tortoise.

На их форуме обсуждалась эта проблема (как я понял, она часто встречается), но решения я не нашел. Если кто-то имел дело с репозиторием WP и может помочь, буду благодарен. Использование Plugin Directory позволит автоматически обновлять плагины _одним кликом_. Ради этого все и делается, в основном.

### Ссылки по теме

  * [Страница плагина](/wp-flipper/) WP Flipper.
  * [Статья Андрея Шитова](http://www.artlebedev.ru/tools/technogrette/) о стрелочной навигации на Техногрете.