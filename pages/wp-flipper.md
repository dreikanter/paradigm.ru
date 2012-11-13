title: WP Flipper  плагин для стрелочной навигации
link: http://paradigm.ru/wp-flipper
creator: admin
description: 
post_id: 341
post_date: 2008-05-22 12:19:56
post_date_gmt: 2008-05-22 09:19:56
comment_status: open
post_name: wp-flipper
status: publish
post_type: page

# WP Flipper  плагин для стрелочной навигации

Плагин Wordpress Flipper (WPF) предназначен для навигации по страницам блога с помощью горячих клавиш Ctrl-right/Ctrl-left.

![](http://b.paradigm.ru/2008/05/ctrl.jpg)

### Где взять?

Здесь: [http://things.paradigm.ru/wp-flipper.zip](http://things.paradigm.ru/wp-flipper.zip).

### Как установить?

Для установки плагина, директорию `wp-flipper` из архива необходимо распаковать в каталог `/wp-content/plugins/`, после чего активировать плагин в панели управления Wordpress. Рекомендуется использовать версию Wordpress не ниже 2.5.

![](http://b.paradigm.ru/2008/05/wp-flipper-active.png)

### Как работает?

Очень просто. После того, как плагин активирован, появляется возможность использовать комбинации клавиш Ctrl-left и Ctrl-right для перехода между страницами ленты блога, а так же страницами архива и отдельными постами. Никакой настройки плагин не требует.

### Все пропало?

Если после активации плагина, горячие клавиши не срабатывают, наиболее вероятной причиной может быть отсутствие вызова `wp_footer()` в вашей теме оформления WP. В том случае, если эта фраза понятно, вы сами прекрасно справитесь с добавлением этого вызова. Если же она вам ничего не говорит, можно попробовать вызвать слесаря по WP или просто сменить тему. Например, в стандартной теме Кубрик все проверенно работает.

Другой причиной может быть использование какой-либо несовместимой (возможно, старой) версии браузера. В таком случае, браузер лучше обновить --- это и для здоровья полезно. Работа плагина проверена на последних версиях IE, Firefox и Opera.

### Как поддержать мировое равновесие?

Плагин бесплатный, но, если он вам понравился, выразить автору спасибо и мотивировать дальнейшее развитие и поддержку проекта можно очень простым способом:

**Карма +1**

![](https://img.yandex.net/i/money/bottom-default.gif)

Webmoney: Z193699171557; Яндексденьги: 4100141741704. PR в виде линков на плагин так же приветствуется.

### Спасибо

Андрею Шитову за [пример JavaScript кода](http://www.artlebedev.ru/tools/technogrette/), который очень пригодился.

## Comments

**[bur](#47345 "2010-09-10 13:09:13"):** Спасибо за плагин Но у меня он почему-то работает при нажатии только стрелки, без ctrl. Очень неудобно Как добавить обработку клавиши ctrl?
