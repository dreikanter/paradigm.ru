title: Приручаем Google Chart API
link: http://paradigm.ru/google-chart
creator: admin
description:
post_id: 88
created: 2007/12/18 01:12:04
created_gmt: 2007/12/17 22:12:04
comment_status: open
post_name: google-chart
status: publish
post_type: post

# Приручаем Google Chart API

В начале этого месяца, [большой брат](http://google.com) открыл новый интересный сервис для генерации красивых диаграмм — Google Chart. Эти графики по своему оформлению и функциональности очень близки к тому, что мы уже видели в [Spreadsheets](http://docs.google.com/), но теперь появилась возможность генерировать их автономно.

![chart.png](/media/chart.png)

Программный интерфейс у сервиса достаточно прост: все параметры отрисовки диаграммы и данные для визуализации передаются на сервер Google в виде GET запроса (проще говоря, специального URL), а сервер в ответ отдает PNG с нарисованным графиком. По скорости процесс сравним с загрузкой статических файлов, кроме того правила его использования не накладывают никаких жестких ограничений на количество обращений с одного хоста. Все это позволяет очень легко интегрировать Chart в свои системы, да еще и бесплатно снять с собственного сервера дополнительную нагрузку по генерации графики.

Гибкость возможностей Chart API послужила причиной разростания количества параметров URL. Ниже будут приведены несколько примеров того, как с помощью серверных скриптов облегчить задачу по формированию запросов к сервису.

### Упаковка данных

В Chart API предлагается три варианта представления данных, на основе которых будут строиться диаграммы. Самый простой из них — перечисление значений через запятую. Но для большей компактности URL, так же можно кодировать числа одним из двух способов, в результате чего объем передаваемых данных сократится.

Самым эффективным способом кодирования, который мы и будем использовать, является т.н. «[extended encoding](http://code.google.com/apis/chart/#extended)». Метод сводится к последовательной записи двузначных чисел в шестидесятичетырехразрядной (прости меня, русский язык) системе счисления. Динамический диапазон таких значений находится в интервале от 0 до 4095 (64² — 1). Для их представления в кодированном виде, согласно предписанию документации, необходимо использовать строчные и прописные английские буквы, цифровые символы, а так же тире и точку (всего набирается как раз 64 символа). В том случае, если значение не задано, оно будет обозначаться последовательностью из двух «_».

Следующая функция, написанная на PHP, выполняет преобразование десятичных чисел в extended encoding для Google Chart:

    function google_ext_encode($dec) {
        if($dec === false) return '__';

        $e = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.
            'abcdefghijklmnopqrstuvwxyz0123456789-.';
        $m = strlen($e); // == 63
        $res = '';

        do {
            $r = $dec % $m;
            $dec = (int)($dec / $m);
            $res = $e[$r].$res;
        } while($dec > 0);

        $res = sprintf("%'A2s", $res);
        return $res;
    }

Тест:

    $test = array(0, 25, 26, 51, 4093, 4094, 4095);
    echo '<pre>';
    printr_r(array_map("google_ext_encode", $test));
    echo '</pre>';


Программа выдаст несколько закодированных чисел, с аналогичными приведенным в документации значениями: `AA`, `AZ`, `Aa`, `Az`, `.9`, `.-`, `..`.

### Поддержка русского языка

Еще до начала первых экспериментов с Google Chart, у меня было подозрение на тему того, что в нем может не оказаться поддержки русского алфавита (и вообще любых языков кроме английского). Как выяснилось, подозрения были напрасны: API прекрасно поддерживает UTF-8.

HTML код для вставки графика в веб страницу удобно генерировать с помощью специальной функции, пример реализации которой приведен далее. Внутри нее выполняется корректное преобразование текста для надписей:

    function chart_html($type, $width,
        $height, $data, $labels = false, $colors = false, $alt = '') {

        $edata = implode('', (array_map("google_ext_encode", $data)));
        $url = 'http://chart.apis.google.com/chart?cht='.$type.
            '&chd=e:'.$edata.'&chs='.$width.'x'.$height;

        if(is_array($colors) && count($colors))
            $url .= '&chco='.implode(',', $colors);

        if(is_array($labels) && count($labels))
            $url .= '&chl='.implode('|', (array_map("urlencode", $labels)));

        return '<img src="'.$url.'" width="'.$width.'" height="'.$height.
            '" alt="'.$alt.'" />';
    }

Стоит обратить внимание на две детали:

  1. Функция не задействует все параметры, доступные в Charts API, а работает только с наиболее актуальными. Она была написана для генерации круговых диаграмм под конкретную задачу. Для иных типов графиков логичным шагом будет корректировка набора аргументов.
  2. Файл с кодом сохранен в кодировке UTF-8. При хранении исходников и данных в 8-битной кодировке, понадобится дополнительное преобразование текста в UTF, чтобы не-английские буквы не отображались на диаграммах как вопросительные знаки.

Функция `chart_html()` будет выдавать XHTML теги `img`, в соответствии с заданными параметрами:

## Comments

**[Kupuyc](#73 "2007/12/18 02:18:16"):** А вы не изучали возможность рисовать графики со смещенным началом координат? К примеру, когда область определения: [300, 400], а область значений: [120, 600] и чтобы не позволять графику "уезжать" вправо вверх начало координат смещают, например в (250, 100).

**[admin](#74 "2007/12/18 04:46:37"):** Автоматической подстройки координатной сетки под график всегда можно добиться на этапе предобработки данных: ищем максимумы и задаем по ним размерность диаграммы. "Двигать" окно по координатной плосскости тоже не сложно, учитывая, что линии координат в Chart API носят очень условный характер. Для отметок на них можно задавать произвольные значения, и не обязательно нумеровать их начиная с 0.

**[Kupuyc](#75 "2007/12/18 08:00:49"):** Это вы все верно сказали, но и ответили на другой вопрос ;).

**[Anton Shevchuk](#76 "2007/12/18 14:02:08"):** Создал небольшой сервис для генерации URL: [http://charts.hohli.com](http://charts.hohli.com) (пока альфа версия, постараюсь в дальнейшем перекрыть весь функционал Google Charts API), так что если необходимо единоразово построить график для блога - то милости прошу...

**[ads](#77 "2007/12/18 14:28:13"):** Молодца!

**[admin](#78 "2007/12/18 14:52:06"):** Anton Shevchuk: Удобная штука. Для разовой отрисовки диаграмм выглядит проще в обращении, чем спредшитс.

**[Via_Lactea](#99 "2007/12/20 00:36:18"):** а есть эта функция кодирования для C#? очень надо.

**[admin](#100 "2007/12/20 01:30:30"):** На C# - нет. А в чем проблема самостоятельно сделать? Переписать ее на C# можно за несколько минут.

**[marmeladnoe](#3939 "2008/10/24 15:48:08"):** $e = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'. 'abcdefghijklmnopqrstuvwxyz0123456789-.'; $m = strlen($e); // == 63 echo $m; Выведет 64.

**[marmeladnoe](#3941 "2008/10/24 16:00:09"):** ... printr_r(array_map("google_ext_encode", $test)); printr_r нет такой функции =) $res = sprintf("%'A2s", $res); return $res; меняем на return sprintf("%'A2s", $res);

**[Serjio](#11697 "2008/12/16 19:10:28"):** Не получается внедрить это в c#, помогите плиз!

**[Никита Балакирев](#45055 "2010/04/24 16:55:50"):** По поводу генерации javasctipt кода написал небольшую программу на QT. Возможно она вас заинтересует. [Программа на QT](http://lifeisfine.ru/2010/04/programmgooglecapi/) Также рассмотрен небольшой пример взаимосвязи [PHP+MYSQL+Google chart API (javascript)](http://lifeisfine.ru/2010/04/google-chart-api-mysql-php/) жду отзывов и предложений

**[mstar](#25779 "2009/02/17 19:21:20"):** Спасибо автору, поборол таки русский текст на графиках.

**[botanik_tipo](#57766 "2011/07/19 15:03:01"):** Ya pitayus poslat dannie iz mysql db i sdelat pie chart s pomojyu javi! pomogite! ponatie ne imeyu, kak eto sdelat!

