title: WP 2.5: Подробности о цветовых схемах адмнистративного UI
link: http://paradigm.ru/wp25-admin-color-schemes
creator: admin
description: 
post_id: 234
post_date: 2008-03-21 03:05:58
post_date_gmt: 2008-03-21 10:05:58
comment_status: open
post_name: wp25-admin-color-schemes
status: publish
post_type: post

# WP 2.5: Подробности о цветовых схемах адмнистративного UI

В [предыдущем посте](/2008/03/18/wp25-rc1-preview/) о Wordpress я рассказывал о том, что появилась возможность настраивать индивидуальные цветовые схемы административного интерфейса. А еще делал предположение о том, что стандартный набор из двух возможных вариантов можно будет расширять. Это оказалось действительно так.

Один из девелоперов WP  [Ryan](http://ryan.wordpress.com/) [сказал](http://b23.ru/3b4 ) на форуме разработчиков, что «New color schemes can be added as plugins». Сама «выбиралка» схем на странице настроек пользовательского профиля выглядит следующим образом:

![](/;-\)/2008/03/0-colorschemes.png)

А «изнутри» переключение между ними осуществляется с помощью новой функции `wp_admin_css_color()`. Ее описание было найдено на блоге [planetOzh](http://b23.ru/3bt):
    
    wp_admin_css_color(
        $shortname, // eg 'mycoolstyle', used to
                 //store setting in the wp_options table
        $longname, // eg 'My Cool Style'
        $css_url, // URL to your custom stylesheet
        array(
            $color_code_1, // a HTML color code like '#123456'
            $color_code_2, // or 'red'
            $color_code_3, // or 'rgb(100,100,100)'
            ... // any number of color codes you want
        )
    );

Здесь `$shortname`  идентификатор цветовой схемы для таблицы `wp_options`; `$longname`  обычное название схемы для отображения в административном UI, `$css_url`  URL соответствующего CSS-файла; последний аргумент  массив, который может содержать произвольное количество используемых в схеме цветов (подходит любой CSS-совместимый формат записи).

А вот пример самого плагина оттуда же: 
    
    <?php
    /*
    Plugin Name: Admin Custom CSS: Brazil!
    Plugin URI: http://planetozh.com/blog/
    Description: Plugin example: add a per-user custom CSS to the admin area
    Author: Ozh
    Version: 0.1
    Author URI: http://planetozh.com/
    */
    
    add_action('admin_init','custom_admin_css_brazil');
    
    function custom_admin_css_brazil() {
        $plugin_url = get_option( 'siteurl' ) . '/wp-content/plugins/' .
            plugin_basename(dirname(__FILE__)) ;
        wp_admin_css_color(
            'brazil',
            __('Brazil'),
            $plugin_url . '/wp-admin-brazil.css',
            array(
                '#3c940c',
                '#fffc01',
                '#3005eb',
            )
        );
    }
    ?>

Весьма просто и понятно.

Что удивительно, еще до появления релиза WP 2.5, один «настоящий» плагин такого типа уже нашелся. Это даже не просто цветовая схема, а полноценная альтернативная «тема» административного интерфейса  [Fluency Admin](http://b23.ru/3bb).

Автор пишет, что был очень расстроен несовместимостью любимого им [Tiger](http://orderedlist.com/wordpress-plugins/wp-tiger-administration/) с новой версией CMS, и решил сделать собственную альтернативу. Я сам не являюсь любителем «Тигра», но Fluency визуально весьма понравился. Строгий, уравновешенный черно-белый дизайн (без дебильного глянца и других модных деталей). Чем-то похож на iTunes.

![Fluency Admin](/;-\)/2008/03/1-fluency-admin-theme.jpg)

Пока я не стал примерять эту тему (отложим до выхода релиза WP), но главная мораль сказки заключается в том, что админка WP становится более гибкой, чем была ранее. И это хорошая тенденция.

## Comments

**[Климов](#426 "2008-03-22 11:10:40"):** Прикол, на самом деле почти ничего не изменилось. (

