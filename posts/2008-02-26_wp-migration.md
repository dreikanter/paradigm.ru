title: Перенос Wordpress на новый сервер
link: http://paradigm.ru/wp-migration
creator: admin
description: 
post_id: 190
created: 2008/02/26 19:42:04
created_gmt: 2008/02/26 16:42:04
comment_status: open
post_name: wp-migration
status: publish
post_type: post

# Перенос Wordpress на новый сервер

Недавно выяснилось, что миграция Wordpress на новый сервер — отнюдь не такая банальная задача, как это казалось изначально. В принципе, очень редко бывает, чтобы перенос CMS проходил идеально гладко. Исключением из этого правила, которое я сейчас помню, была разве что DokuWiki, хранящая все данные в файлах. Ее можно было просто скопировать, после чего wiki продолжала работать. Что же касается Wordpress, первая попытка переноса закончилась потерей тагов, которые по неведомой причине заменились на свои ID. Кроме того, в списке категорий почему-то появились повторяющиеся значения.

Это означает, что Wordpress до последней версии так и не научился самостоятельно делать нормальный дамп базы данных. Возможно, это как-то связано с модификациями в таблицах, сделанных плагинами, тем не менее, при миграции в любом случае придется искать альтернативный более надежный способ. 

Таких способов существует немало. Один из них, который мне наиболее понравился, описан далее по тексту. Для него потребуется возможность использования SSH на обоих серверах — старом и новом. Итак:

  * Заходим по SSH на сервер, с которого переносим Wordpress.
  * Делаем дамп базы данных:  
`mysqldump -u username -ppassword database > wp.sql`  
`username`, `password` и `database` заменяются соответственно на имя пользователя, пароль и название переносимой БД. После параметра `-p` нет пробела.
  * Упаковываем дамп и все файлы Wordpress в архив:  
`tar -czf /path/to/wordpress/wp.tar.gz`  
Архив должен находиться в видимой из web директории сервера.
  * Заходим по SSH на сервер, куда переносим Wordpress.
  * С помощью `wget` скачиваем архив с файлами Вордпресса и дампом БД (во временную директорию, не видимую из web):  
`wget http://source.com/wp.tar.gz`
  * Удаляем архив на первом сервере, т.к. оставлять его в открытом доступе небезопасно:  
`rm /path/to/wordpress/wp.tar.gz`
  * Распаковываем скаченный архив на новом сервере:  
`tar -xzf wp.tar.gz`
  * Импортируем дамп в БД:  
`mysql -u username -ppassword -D database < wp.sql`  
Параметры те же, что и при экспорте дампа.
  * После импорта, файл можно удалить:  
`rm wp.sql`
  * Переносим файлы Вордпресса в web root (видимую из web директорию сервера):  
`mv /home/user/tmp/* /home/user/public_html`  
(пути, естесственно, заменяются на акуальные для конкретного сервера)
  * Открыть в текстовом редакторе файл `wp-conf.php` и заменить в нем параметры подключения к серверу БД (если они изменились).
  * Далее понадобится перенести домен, т.е.зайти на сайт регистратора и заменить в административной панели адреса DNS серверов новыми значениями.

В том случае, если при миграции так же меняется URL сайта, понадобится выполнить ряд дополнительных манипуляций. Сложность в том, что Вордпресс хранит некоторые свои параметры в базе данных.

Для смены адреса сайта, необходимо с помощью phpMyAdmin (или любого другого инструмента для работы с БД) заменить значение параметра `siteurl` в таблице `wp_options` новым значением корневого URL.

### Ссылки по теме

  * [How to move a WordPress blog to a new URL or domain](http://b23.ru/33v) @ Clearpoint Systems blog — проиллюстрированная скриншотами инструкция по смене URL блога на Вордпрессе.
  * [Changing The Site URL](http://codex.wordpress.org/Changing_The_Site_URL) @ Wordpress Codex — официальная инструкция по переносу Вордпресса.

## Comments

**[Alex](#339 "2008/02/27 03:17:20"):** Хм.. а зачем такие сложности? Я вот использовал Sypex Dumper, еще со времен администрирования одного форума на invision. Пока что еще ни разу меня этот скрипт не подводил, в отличии от всяких встроенных дамперов и средств хостера.

**[Mister](#341 "2008/02/27 10:33:19"):** Я тоже думаю, что зря все эти заморочки.

**[Ink](#357 "2008/03/05 09:00:06"):** Зря вы это делаете...

**[Виктор](#681 "2008/05/04 12:04:45"):** пользуйтесь Sypex Dumper, и проблем никогда не будет

**[onceagain](#1457 "2008/07/28 20:14:21"):** Еще поменять значение home в wp_options Сложнее когда два блога используют одну и ту же базу но находятся на разных доменах.

**[onceagain](#1460 "2008/07/28 23:25:44"):** Мое решение такое: В файле wp-blog-header.php перед вызовом функци wp(); добавляем: $vars_site_directory_printable=$_SERVER['HTTP_HOST'].dirname($_SERVER['PHP_SELF']); $vars_site_directory="http://".preg_replace("/^\//","",$vars_site_directory_printable); if (get_option('siteurl')!="${vars_site_directory}/") { update_option('siteurl',$vars_site_directory."/"); } if (get_option('home')!="${vars_site_directory}") { update_option('home',$vars_site_directory); } Дальше должно быть: wp();

**[apmucm](#38499 "2009/10/04 15:18:52"):** Незнаю, как и чё, я опишу свой "ламерский" способ переноса, как мне показалось меньше мороки... Делаю DAMP базы на старом хосте в формате .SQL - вставляю данные в .DOC и редактирую относиетельные пути и название домена методом замены. Обсолютные пути можно посмотерть в панели управления хостом, если кто не в курсе (где именно... это к хостеру, хотя метод научного тыка никто не отменял). Дальше выполняю SQL-запрос на новом хосте в phpMyAdmin. Редактирую siteurl в таблице wp_options. Копирую все файлы на локалку - пезеливаю файло на новый хост. Редактирую wp-config.php в соответсвии с настройками нового хостинга. Захожу и любуюсь. Мне проще так. :D Не изучал суперДАМПЕР и потому таким вот образом за 15 минут манипуляций, всё переносится на ура. :) Если кому не помогло, не ругайтесь, проверьте руки на предмет абсолютной кривизны или же ищите другой. :D

