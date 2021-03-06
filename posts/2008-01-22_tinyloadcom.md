title: Человеческое лицо для файловых хостингов
link: http://paradigm.ru/tinyloadcom
creator: admin
description:
post_id: 162
created: 2008/01/22 17:13:25
created_gmt: 2008/01/22 14:13:25
comment_status: open
post_name: tinyloadcom
status: publish
post_type: post

# Человеческое лицо для файловых хостингов

Опыт использования многочисленных файловых хостингов типа ставшей недавно [козлом отпущения](http://b23.ru/nca) rapidshare.com сформировал о них стереотипное мнение:

  * **Низкая надежность.** С любого хостинга файлы удаляются по истечению временного лимита или определенного в правилах использования периода неактивности. Кроме того, нет никакой гарантии, что файлы не удалятся в любой момент без объяснения причин. Сервисы бесплатны — соответственно никаких претензий на эту тему быть не может.
  * **Капча.** И множество иных способов борьбы с роботами и чрезмерно жадными до трафика пользователями. В принципе, меры безопасности оправданы, но на деле они становятся дополнительными помехами использования сервисов. Например, той же Rapidshare практически невозможно пользоваться через NAT, из относительно-большого сегмента сети (сервис постоянно сообщает, что лимит трафика для общего IP исчерпан).
  * **Топором-деланый веб-интерфейс.** Даже при отсутствии банеров, дизайн подобных ресурсов зачастую заставляет зажмуриться. Впрочем, [есть](http://drop.io) [исключения](http://zalil.ru).

Не смотря на все недостатки, альтернатив подобным сервисам во многих случаях просто нет. Точнее, они неконкурентноспособны.

Наиболее технологичное решение — пиринговые сети, но они по-совместительству так же являются и самым сложным решением. Основная сложность в том, что 9 из 10 пользователей сети просто не знают, что это такое и как оттуда забирать файлы. Кроме того, далеко не всегда есть возможность достаточно долго держать хост включенным, да и сам процесс выкладывания файла в общий доступ еще ни в одном клиенте не сделан удобно (обычно требуется много рутинных операций).

FTP сервер — хороший, но не всегда доступный вариант, т.к. во-первых под рукой может не быть FTP-клиента. Во-вторых, покупка качественного FTP хостинга бывает экономически-неоправданной (для одноразового использования, например), а малобюджетный или бесплатный хостинг скорее всего будет сильно тормозить или вообще обвалится под нагрузкой.

![](/media/tinyload-logo.png)

Сегодня был найден сервис «второго уровня» (сервис для работы с сервисами), который в некоторой степени облегчает работу с файловыми хостингами. С помощью [tinyload.com](http://tinyload.com) можно автоматически заливать файл сразу на несколько сервисов, получая единую короткую ссылку на веб-страницу со списком зеркал — простой и вполне работающий псособ повышения надежности хостинга. Сочетание функциональности сервиса с тем, что его интерфейс выполнен достаточно аккуратно (банеров на страницах сайта, кстати, нет), делает его вполне работоспособным и оправданным в использовании инструментом.

Об интерфейсе стоит рассказать подробнее. Во-первых, у сервиса есть букмарклет, дающий возможность не «отвлекаться» от загруженной в браузере страницы, выполняя загрузку файлов.

![](/media/tinyload-bookmarklet.png)

Но гораздо более интересной возможностью является открытый API. Т.е. фактически tinyload.com дает возможность автоматически загружать файлы на все поддерживаемые им сервисы (MegaUpload, MediaFire, RapidShare, DivShare, BestSharing, SendSpace, Uploading, FileSend, EasyShare, Amazon S3).

API достаточно прост в использовании и расчитан на зугрузку файлов с внешних сайтов по HTTP (FTP к сожалению не поддерживается). Обращения к сервису выполняются в виде HTTP GET запросов следующего вида:

`http://tinyload.com/api/1.0/transload.{format}?{parameters}`

Где `{format}` — один из возможных вариантов формата ответа сервера (`xml`, `json`, `txt`), а `{parameters}` — список параметров:

  * `url` (обязательный параметр) — адрес файла, который подлежит загрузке;
  * `name` — альтернативное имя, которое можно задать файлу;
  * `force` — определяет, будут ли файлы с одинаковыми именами перезаписываться. Не вполне понятный по своему краткому описанию параметр, т.к. ни на каком хостинге файлы с одинаковыми именами все равно не записываются поверх друг друга. Возможно, речь шла о внутреннем кэше tinyload.com, где файлы тоже какое-то время хранятся. Значения: `true`/`false` (по-умолчанию — `false`).
