title: Два хороших способа предзагрузки графики
link: http://paradigm.ru/img-preloading
creator: admin
description: 
post_id: 136
post_date: 2007-12-27 00:17:55
post_date_gmt: 2007-12-26 21:17:55
comment_status: open
post_name: img-preloading
status: publish
post_type: post

# Два хороших способа предзагрузки графики

Скорость отображения страниц  один из важных показателей общего впечатления от сайта, которое возникает у его посетителей. Существенное влияние на этот показатель оказывает количество графики на веб-страницах, а так же оптимальность ее использования.

Одним из распространенных способов увеличения скорости открытия страниц является предзагрузка графикических файлов. Это полезно, когда на сайте используются типовые элементы дизайна (например, навигационные меню c [ролловерами](http://b23.ru/clr)), для которых имеет смысл за один раз загрузить всю графику, чтобы она осталась в кэше браузера.

В блоге Perishable Press я нашел два способа реализации предзагрузки, чье описание со своими изменениями и комментариями привожу ниже. 

### Предзагрузка веб-графики с помощью CSS

Этот метод особенно понравился минимализмом и очевидностью. Его суть заключается во вставке в HTML код перечня всех картинок, подлежащих единовременной загрузке. При этом для всех тегов img, описывается специальный CSS класс, благодаря которому соответствующие им картинки не будут отображаться.

CSS: 
    
    img.preload { display: none; }

(X)HTML: 
    
    <img src="image1.jpg" alt="" class="preload" />
    <img src="image2.jpg" alt="" class="preload" />
    <img src="image3.jpg" alt="" class="preload" />
    <img src="image4.jpg" alt="" class="preload" />

Обратите внимание на набор атрибутов img: width и height отсутствуют, т.к. в отличии от обычной вставк графики, в данном случае нет смысла задавать размеры. Картинки все равно не будут отображены, поэтому и опциональные атрибуты можно отбросить. Обязательному атрибуту alt задано пустое значение, т.к. функциональной нагрузки он не несёт и никакой текстовой замены для него предусматривать не нужно.

Теги размещаются в самом конце кода страницы, непосредственно перед . Это вынудит браузер загрузить вначале содержательную часть веб-документа, а предзагрузку дополнительной графики оставить «на постом». Благодаря этому, скорость отображения страницы не пострадает, увеличится только время первой загрузки.

Предзагрузка с помощью JavaScript Недостаток предыдущего способа состоит в том, что если в браузере нет поддержки CSS или она намеренно выключена, кэшируемые картинки дружно отобразятся в виде бесформенной кучи.

Альтернативный способ основан на JavaScrpt. Он тоже достаточно прост, и при том никак не повредит внешнему виду сайта, даже если у пользователя по какой-то причине отключен JS (правда, в таком предзагрузка не произойдет вообще).

Код приведенного ниже скрипта можно вставить в любую часть страницы, например в контейнер head.
    
    <script type="text/javascript">
    if (document.images) {
    
       img1 = new Image();
       img2 = new Image();
       img1.src = "/img/image01.gif";
       img2.src = "/img/image02.gif";
    
    }
    </script>

Имена перечисленных файлов, конечно же, необходимо заменить актуальными значениями, после чего они будут загружены в кэш при первом открытии страницы в браузере и в дальнейшем станут доступны без временн_ы_х затрат на обращение к серверу.

Ссылки по теме: 

  * [Оригинальная статья](http://perishablepress.com/press/2006/11/14/preloading-images-with-css-and-javascript/) на Perishable Press
  * [Rollover](http://b23.ru/clr) @ Wikipedia

## Comments

**[pepelsbey](#137 "2007-12-27 00:45:38"):** Насколько мне известно, существует особенность, при которой браузер не загружает картинки, которым назначен (или унаследован) display:none… Давно не занимался этими вещами, так что запамятовал — не то Opera, не то ещё кто-то. Второй JS-способ было бы логичнее завернуть в функцию массивом картинок на входе и циклом внутри. Ну и проверять document.images на исходе 2007 года… это забавно )

**[admin](#140 "2007-12-27 01:38:54"):** pepelsbey: По поводу display:none у меня тоже были подозрения. По хорошему, надо бы это протестировать, но даже если в каких-то браузерах способ не работает, думаю, можно решить вопрос вариациями на тему. Например, задавать display:none не самим img, а контейнеру, в они будут храниться. Или уменьшить их до одного пикселя и поиграть с позиционированием - "задвинув" под какой-нибудь слой. По поводу использования функции во втором способе - согласен, т.к. картинок обычно не две, как в примере. Массив картинок для нее вообще хорошо было бы как-то генерировать на серверной стороне, чтобы свести человеческий фактор к минимуму (если составлять список, наверняка какая-то из них окажется забытой или лишней). Можно, например, помечать картинки для предзагрузки специальным префиксом и в коде шаблонизатора генерить массив имен файлов прямо в виде JS.
