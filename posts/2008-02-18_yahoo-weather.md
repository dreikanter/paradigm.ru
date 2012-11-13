title: Поговорим о погоде
link: http://paradigm.ru/yahoo-weather
creator: admin
description: 
post_id: 181
created: 2008/02/18 17:19:01
created_gmt: 2008/02/18 14:19:01
comment_status: open
post_name: yahoo-weather
status: publish
post_type: post

# Поговорим о погоде

За последнее время мне пришлось довольно основательно пообщаться с несколькими сервисами Yahoo! В этой заметке расскажу об одном из них, который для многих может оказаться полезным. Это сервис прогноза погоды [Yahoo! Weather](http://weather.yahoo.com/).

Года два назад я искал в сети удобный и открытый источник подобных данных, но так ничего и не нашел. Все отечественные сервисы экспортировали данные за деньги (отпугивала скорее не цена, а наличие возни с оплатой), а западные  либо не предоставляли данных в удобном для автоматической обработки формате, либо не знали о существовании города, в котором я живу.

Yahoo! Weather порадовал отсутствием перечисленных недостатков. Информация о текущей погоде, а так же прогноз на ближайшие 5 дней экспортируется с их сервера в формате RSS. На мой взгляд, выбор этого формата притянут за уши, т.к. погодная RSS «лента» содержит всего однин item. Те же данные можно было бы экспортировать в более лаконичном виде, но в Yahoo, по всей видимости, расчитывали на стандартные методы обработки RSS, что тоже довольно весомый довод. Стандарт  это все-таки стандарт.

Адрес RSS фида для интересующего города можно найти на сайте [weather.yahoo.com](http://weather.yahoo.com/).

![](/;-\)/2008/02/yahoo-weather.png)

В общем виде URL выглядит следующим образом:

`http://xml.weather.yahoo.com/forecastrss?p=**код_города**&u=**единицы_измерения**`

Первый параметр  идентификатор интересующего города. Второй параметр определяет, в каких единицах будет выдаваться температура. Значение «c» соответствует градусам Цельсия, «f»  градусам Фаренгейта. 

Несколько примеров: 

  * Москва  [http://xml.weather.yahoo.com/forecastrss?p=RSXX0063&u=c](http://xml.weather.yahoo.com/forecastrss?p=RSXX0063&u=c)
  * Санкт-Петербург  [http://xml.weather.yahoo.com/forecastrss?p=RSXX0091&u=c](http://xml.weather.yahoo.com/forecastrss?p=RSXX0091&u=c)
  * Екатеринбург  [http://xml.weather.yahoo.com/forecastrss?p=RSXX1219&u=c](http://xml.weather.yahoo.com/forecastrss?p=RSXX1219&u=c)
  * Ханты-Мансийск  [http://xml.weather.yahoo.com/forecastrss?p=RSXX1234&u=c](http://xml.weather.yahoo.com/forecastrss?p=RSXX1234&u=c)
  * Киев  [http://xml.weather.yahoo.com/forecastrss?p=UPXX0016&u=c](http://xml.weather.yahoo.com/forecastrss?p=UPXX0016&u=c)

Описанный ниже класс `YahooWeather` (PHP5) предназначен для загрузки RSS по HTTP и предварительной обработки данных.
    
    <?php
    
    class YahooWeather {
        // Ветер
        public $wind_chill;
        public $wind_direction;
        public $wind_speed;
    
        // Атмосферные показатели
        public $humidity;
        public $visibility;
        public $pressure;
    
        // Время восхода и заката переводим в формат unix time
        public $sunrise;
        public $sunset;
    
        // Текущая температура воздуха и погода
        public $temp;
        public $condition_text;
        public $condition_code;
    
        // Прогноз погоды на 5 дней
        public $forecast;
    
        public $units;
    
        function __construct($code, $units = 'c', $lang = 'en') {
            $this->units = ($units == 'c')?'c':'f';
    
            $url = 'http://xml.weather.yahoo.com/forecastrss?p='.
                $code.'&u;='.$this->units;
    
            $xml_contents = file_get_contents($url);
            if($xml_contents === false) 
                throw new Exception('Error loading '.$url);
    
            $xml = new SimpleXMLElement($xml_contents);
    
            // Ветер
            $tmp = $xml->xpath('/rss/channel/yweather:wind');
            if($tmp === false) throw new Exception("Error parsing XML.");
            $tmp = $tmp[0];
    
            $this->wind_chill = (int)$tmp['chill'];
            $this->wind_direction = (int)$tmp['direction'];
            $this->wind_speed = (int)$tmp['speed'];
    
            // Атмосферные показатели
            $tmp = $xml->xpath('/rss/channel/yweather:atmosphere');
            if($tmp === false) throw new Exception("Error parsing XML.");
            $tmp = $tmp[0];
    
            $this->humidity = (int)$tmp['humidity'];
            $this->visibility = (int)$tmp['visibility'];
            $this->pressure = (int)$tmp['pressure'];
    
            // Время восхода и заката переводим в формат unix time
            $tmp = $xml->xpath('/rss/channel/yweather:astronomy');
            if($tmp === false) throw new Exception("Error parsing XML.");
            $tmp = $tmp[0];
    
            $this->sunrise = strtotime($tmp['sunrise']);
            $this->sunset = strtotime($tmp['sunset']);
    
            // Текущая температура воздуха и погода
            $tmp = $xml->xpath('/rss/channel/item/yweather:condition');
            if($tmp === false) throw new Exception("Error parsing XML.");
            $tmp = $tmp[0];
    
            $this->temp = (int)$tmp['temp'];
            $this->condition_text = strtolower((string)$tmp['text']);
            $this->condition_code = (int)$tmp['code'];
    
            // Прогноз погоды на 5 дней
            $forecast = array();
            $tmp = $xml->xpath('/rss/channel/item/yweather:forecast');
            if($tmp === false) throw new Exception("Error parsing XML.");
    
            foreach($tmp as $day) {
                $this->forecast[] = array(
                    'date' => strtotime((string)$day['date']),
                    'low' => (int)$day['low'],
                    'high' => (int)$day['high'],
                    'text' => (string)$day['text'],
                    'code' => (int)$day['code']
                );
            }
        }
    
        public function __toString() {
            $u = "°".(($this->units == 'c')?'C':'F');
            return $this->temp.' '.$u.', '.$this->condition_text;
        }
    }
    
    ?>

Пример использования:
    
    <?php
    
    try {
        $weather = new YahooWeather('RSXX0091');
    } catch(Exception $e) {
        echo "Caught exception: ".$e->getMessage();
        exit();
    }
    
    echo '<h1>'.$weather.'</h1>';
    
    echo "<pre>";
    print_r($weather);
    echo "</pre>";
    
    ?>

Вывод:

![](/;-\)/2008/02/weather-report.png)

## Comments

**[electrocat](#321 "2008/02/19 22:58:09"):** Спасибо !

**[electrocat](#325 "2008/02/20 20:51:22"):** а вы не переводили на русский эту таблицу condition_code ? :)

**[admin](#326 "2008/02/20 21:20:20"):** electrocat: Не переводил.Была такая мысль, но не было прямой необходимости.

**[somebody](#39712 "2009/11/09 11:16:13"):** если добавите в коне &d= то получите прогноз на несколько дней, макс. 5 дней

**[seotavr](#36074 "2009/07/16 16:43:54"):** А моей диревни тама нет - Уфа.А жаль....

**[GeK@](#35335 "2009/06/17 08:52:15"):** Что то я не понял где взять прогноз на ближайшие 5 дней?

**[Janik](#27047 "2009/02/24 13:31:03"):** О! Спасибо большое.

**[admin](#27028 "2009/02/24 10:52:05"):** Janik: Я встречался с такими кодами как раз в сервисе Yahoo Weather. Как именно расшифровывается Location ID — не знаю, но первые две буквы — идентификатор страны, вторые две — какая-то дополнительная его часть (не идентификатор города). Вроде бы, если вторые два символа не использвуются, они заменяются на XX. Все вместе с номером образует код города. Ну а если немного поискать, можно найти таблицу: http://b23.ru/q2k

**[Janik](#26978 "2009/02/24 04:00:52"):** Здравствуйте! Спасибо за информацию, очень познавательно. Только есть ещё один вопрос: Не доводилось ли Вам встречаться с некой, может быть, таблицей кодов городов, наподобие RSXX0091 и что означают зашифрованные данные в этих ZIP-кодах? Спасибо.

**[Палю тему](#43209 "2010/02/24 00:54:19"):** Пожалуйста пример в студию! Куда именно добавить &d=5 ?

**[Юрий](#48647 "2010/11/15 22:42:54"):** Глупо брать погоду с yahoo, если yahoo берет ее с weather.com откуда ее тоже можно брать в формате xml, что и делает программа conky для Linux. Все в Linux! :)

**[mir46.ru](#50287 "2011/01/26 11:38:31"):** Сейчас тестил, почему-то в массиве $forecast, инфа не на 5 дней а на 2 =\

