title: Оптимизация работы веб-приложений: введение
link: http://paradigm.ru/web-aps-optimization
creator: admin
description: 
post_id: 141
post_date: 2007-12-29 00:53:15
post_date_gmt: 2007-12-28 21:53:15
comment_status: open
post_name: web-aps-optimization
status: publish
post_type: post

# Оптимизация работы веб-приложений: введение

Изначально я предполагал написать пост с обзором нескольких средств повышения производительности веб-приложений, но, чтобы не начинать тему оптимизации с середины, добавить распространенное введение. Но, в процессе написания этого текста, стало понятно, что тема слишком большая, и мне не хочется отбрасывать многие из ее значимых аспектов. Поэтому я отказался от первоначального варианта заголовка («Оптимизация веб-приложений: коротко, о самом интересном») и вместо одного поста напишу серию заметок на эту тему. Этот пост посвящен общему обзору задачи.

Оптимизация работы веб-приложения может рассматриваться в разных аспектах: с точки зрения эффективности пользовательского интерфейса, экономической окупаемости, идеологической адекватности решаемым задачам, а так же в отношении производительности. В данном случае пойдет речь именно о ней.

Производительность любой программной системы оценивается по двум критериям: это скорость и ресурсоемкость. В контексте веб-приложений, скорость определяется максимальным количеством запросов, которое система может стабильно обрабатывать за единицу времени. Основными же показателями ресурсоемкости является нагрузка на процессор (обычно это наиболее ценный ресурс сервера) и объем используемой оперативной памяти.

Рассмотрим этапы работы типового веб-приложения, а затем выделим из них типовые операции, которые в наибольшей степени влияют на производительность системы вцелом. 

### Этапы работы типового веб-приложения

  * Сервер получает HTTP запрос от клиентскй программы.
  * В том случае, елси запросу соответствует статический файл, его содержимое сразу пересылается клиенту, и на этом итерация благополучно завершается. Но, учитывая, что в данный момент речь идет именно о веб-приложениях, нас больше интересует вариант с динамической генерацией страницы. При использовании CMS, появляются дополнительные этапы обработки запроса.
  * Если CMS реализована на интерпретируемом языке, первым из таких этапов будет передача исполняемого кода из ее файла (зачастую, из множества файлов) интерпретатору. Например, им может быть модуль Apache `mod_php` или `mod_python`.
  * Интерпретатор запускает на исполнение обработчик запросов CMS.
  * Если система работает с авторизованными пользователями (например, это веб-магазин или какой-либо социальный сервис, требующий идентификации «активных» посетителей), начало обработки любого запроса будет связано с восстановлением пользовательских данных из cookies или базы данных. Возможны комбинированные варианты, когда одновременно используются куки и считывание дополнительные данных из таблицы пользователей, либо сами куки хранятся в БД.
  * Дальнейшая обработка запроса обычно предполагает необходимость выдать контент или проанализировать и сохранить принятые данные. Вне зависимости от того, происходит ли генерация страницы или обработка данных из формы, понадобится взаимодействие с БД.
  * Практически в любой CMS используется свой обработчик темплейтов, используемый на последнем этапе обработки запроса. Он получает на вход данные и шаблоны, и выдает сгенерированные по ним веб-страницы. В простейшем случае синтаксический разбор шаблона подразумевает обычную замену специальных маркеров в его тексте (макросов) на фрагменты контента. Но зачастую используются более мощные шаблонизаторы, которые используют свой собственный синтаксис. В качестве показательного примера можно приветси XSLT парсеры, которые могут быть использованы на этом этапе для генерации XHTML кода веб-страниц из данных в формате XML. В некоторых случаях этап обработки шаблонов так же связан с запросами к БД для вставки в страницы второстепенного контента (такого как списки категорий, ссылки на связанные материалы, иные динамические навигационные элементы, статистическую информацию и т.п.).

Как уже было сказано, на основании перечисленных этапов, можно сформировать список конкретных операций, наиболее значимых в отношении производительности. К ним относятся следующие:

  * Взаимодействие с файловой системой.
  * Интерпретация кода программы.
  * Работа с базой данных.
  * Синтаксический разбор кода шаблонов страниц.
  * Передача данных по сети.