---
layout: default
---

[![Watch the video](https://github.com/fplve/vika/raw/gh-pages/assets/images/chat-bot_original.gif)](https://vk.com/video144351669_456239072)

# Что vk-бот из себя представляет.

<article>Чат-бот – это программа-собеседник имитирующая человеческое общение при помощи текста или голоса.</article>
![бот](https://github.com/fplve/vika/raw/gh-pages/assets/images/listia_kirpichnaia_stena_rastenie_122951_1920x1080.jpg)
<article>Чат-боты помогают автоматизировать задачи, работая по заданному алгоритму. Они ведут диалог с пользователем, выполняя его просьбы, отвечая на запросы или развлекая своими ответами.</article>

-------------------------

<article>
<h2>Модули</h2>
<ul>
<li><a href="#cmdlist">Модуль cmdlist.php</a></li>
<li><a href="#info">Модуль info.php</a></li>
<li><a href="#picture">Модуль picture.php</a></li>
<li><a href="#qanda">Модуль qanda.php</a></li>
<li><a href="#rolling">Модуль rolling.php</a></li>
<li><a href="#tts">Модуль tts.php</a></li>
<li><a href="#weather">Модуль weather.php</a></li>
</ul>
<h2>Авторы</h2>
<ul>
<li><a href="#sanya">Саня</a></li>
<li><a href="#ylya">Юля</a></li>
<li><a href="#yar">Ярик</a></li>
<li><a href="#vlad">Влад</a></li>
<li><a href="#leh">Лёха</a></li>
</ul>
</article>

-------------------------

<a name="cmdlist"></a>
### Модуль cmdlist.php

```
Данный модуль выводит список $text.
```

![инфо](https://github.com/fplve/vika/raw/gh-pages/assets/images/c_list.gif)
```php
<?php
function cmdlist($text, $peer_id) {
    $arrayUsers = getArrayUsers($peer_id);
    shuffle($arrayUsers);
    $countUsers = rand(1, count($arrayUsers));
    if ($countUsers != 1) {
        $randUsers = array_rand($arrayUsers, $countUsers);
    } else {
        $randUsers[0] = array_rand($arrayUsers, $countUsers);
    }
    if (empty($text)) {
        $message = "# Список кого-то там:\r\n";
    } else {
        $message = "# Список {$text}:\r\n";
    }
    $number = 1;
    for ($i = 0; $i < $countUsers; $i++) {
        $user_id = $arrayUsers[$randUsers[$i]];
        $user = json_decode(getUserInfo($user_id));
        $message .= "{$number}. {$user->name} {$user->last_name}\r\n";
        $number++;
    }
    $message .= "# Конец списка";
    return sendMessage($message, null, $peer_id);
}
```

-------------------------

<a name="info"></a>
### Модуль info.php

<article>Данный модуль показывает список всех команд, доступных боту.</article>

![инфо](https://github.com/fplve/vika/raw/gh-pages/assets/images/c_info.gif)
```php
<?php
function info($peer_id) {
	$diff = 0;
	$arrayCommand = array(
		array('/captcha <code>', 'Вводит капчу для tts'),
		array('/list', 'Выводит список кого-то там'),
		array('/list <text>', 'Выводит список <text>'),
		array('/info', 'Показывает справку'),
		array('Бот/Вика/Виктория, <text>?', 'Спросить бота и получить ответ'),
		array('/roll', 'Выбрасывает случайного человека из окна'),
		array('/roll <text>', 'Выбирает случайного человека и присваивает ему <text>'),
		array('/tts <text>', 'Воспроизводит <text> (Яндекс)'),
		array('/weather', 'Показывает погоду в Омске'),
	);
	for ($i = 1; $i <= count($arrayCommand); $i++) {
		if ((int)(count($arrayCommand) / $i) <= 10) {
			$num_command = (int)((count($arrayCommand) / $i) + 1);
			$num_message = $i;
			break;
		}
	}
	for ($i = 0; $i < $num_message; $i++) {
		$n = $i + 1;
		$message = "Список команд #{$n}:\r\n";
		for ($j = 0 + $diff; $j < $num_command + $diff; $j++) {
			if ($arrayCommand[$j][0] != null)
				$message .= "{$arrayCommand[$j][0]} — {$arrayCommand[$j][1]}\r\n";
			else break;
		}
		$res = sendMessage($message, null, $peer_id);
		$diff += $num_command;
	}
	return $res;
}
```

-------------------------

<a name="picture"></a>
### Модуль picture.php

<article>Данный модуль отправляет случайное изображение, из доступных боту.</article>

![картинка](https://github.com/fplve/vika/raw/gh-pages/assets/images/c_pic.gif)
```php
<?php
require_once dirname(__FILE__)."/../core/pixabay_config.php";
function picture($peer_id) {
	$category = array('fashion', 'nature', 'backgrounds', 'science', 'education', 'people', 'feelings', 'religion', 'health', 'places', 'animals', 'industry', 'food', 'computer', 'sports', 'transportation', 'travel', 'buildings', 'business', 'music' );
	$image_type = array("all", "photo", "illustration", "vector" );
	$colors = array("grayscale", "transparent", "red", "orange", "yellow", "green", "turquoise", "blue", "lilac", "pink", "white", "gray", "black", "brown" );
	$data = json_decode(file_get_contents('https://pixabay.com/api/?key=' . PIXABAY_TOKEN . '&per_page=20&image_type=' . $image_type[rand(0, count($image_type))] . '&category=' . $category[rand(0, count($category))] . '&colors=' . $colors[rand(0, count($colors))]));
	do {
		$picture = $data->hits[rand(0, 19)];
		$url_image = $picture->largeImageURL;
		preg_match('/(\w+-\d+_\d+.jpg$)/', $picture->previewURL, $matches);
		$name_image = $matches[1];
	} while ($name_image == NULL)
	$attachment = uploadPhoto($url_image, $name_image, $peer_id)
	return sendMessage(null, $attachment, $peer_id);
}
```

-------------------------

<a name="qanda"></a>
### Модуль qanda.php

<article>Данный модуль случайно отвечает на вопрос.</article>

![картинка](https://github.com/fplve/vika/raw/gh-pages/assets/images/c_pic.gif)
```php
<?php
function questionandanswer($peer_id, $message_id) {
    $user_id = getUserIdByMessage($message_id);
    $user = json_decode(getUserInfo($user_id));
    $answer = rand(1, 2);
    $message = ($answer == 1) ? "Да, {$user->name}." : "Нет, {$user->name}.";
    return sendMessage($message, null, $peer_id);
}
```

-------------------------

<a name="rolling"></a>
### Модуль rolling.php

<article>Данный модуль... что-то делает, я не понял.</article>

![картинка](https://github.com/fplve/vika/raw/gh-pages/assets/images/c_pic.gif)
```php
<?php
function roll($text, $peer_id) {
    $arrayUsers = getArrayUsers($peer_id);
    $numUser = (count($arrayUsers) - 1);
    $user_id = $arrayUsers[rand(0, $numUser)];
    $user = json_decode(getUserInfo($user_id));
    if (empty($text)) {
        $res = sendMessage("{$user->name} {$user->last_name} выпал из окна", null, $peer_id);
    } else {
        $res = sendMessage("{$user->name} {$user->last_name} {$text}", null, $peer_id);
    }
    return $res;
}
```

-------------------------

<a name="tts"></a>
### Модуль tts.php

<article>Данный модуль озвучивает сообщение с помощью технологии Яндекса Cloud SpeechKit.</article>

![картинка](https://github.com/fplve/vika/raw/gh-pages/assets/images/c_pic.gif)
```php
<?php
function tts($text, $peer_id) {
	$arrayEmotion = array('good', 'neutral', 'evil');
	$indexEmotion = rand(0, 2);
	$file = saveVoiceMessage($text, 'jane', $arrayEmotion[$indexEmotion]);
	$attachment = uploadVoiceMessage($file, $peer_id);
	if (!(is_array($attachment))) return sendMessage(null, $attachment, $peer_id);
	else return $attachment;
}
```

-------------------------

<a name="weather"></a>
### Модуль weather.php

<article>Данный модуль возвращает текущую погоду в Омске.</article>

![картинка](https://github.com/fplve/vika/raw/gh-pages/assets/images/c_weather.gif)
```php
<?php
function weather($peer_id) {
    $data = json_decode(file_get_contents("http://api.wunderground.com/api/47e38818810ce9cb/conditions/q/RU/Omsk.json"));
    $temp_city = $data->current_observation->temperature_string;
    $feel_city = $data->current_observation->feelslike_string;
    $name_city = $data->current_observation->display_location->full;
    $message = "{$name_city}\r\nТемпература: {$temp_city}\r\nОщущается: {$feel_city}";
    return sendMessage($message, null, $peer_id);
}
```

-------------------------

<a name="sanya"></a>
## Саня
![картинка](https://github.com/fplve/vika/raw/gh-pages/assets/images/sanya.png)
<article>Тут будет текст про этого человечка, я просто написал вот это, что бы хоть что-то было написано.</article>

-------------------------

<a name="ylya"></a>
## Юля
![картинка](https://github.com/fplve/vika/raw/gh-pages/assets/images/sanya.png)
<article>Тут будет текст про этого человечка, я просто написал вот это, что бы хоть что-то было написано.</article>

-------------------------

<a name="yar"></a>
## Ярик
![картинка](https://github.com/fplve/vika/raw/gh-pages/assets/images/sanya.png)
<article>Тут будет текст про этого человечка, я просто написал вот это, что бы хоть что-то было написано.</article>

-------------------------

<a name="vlad"></a>
## Влад
![картинка](https://github.com/fplve/vika/raw/gh-pages/assets/images/sanya.png)
<article>Тут будет текст про этого человечка, я просто написал вот это, что бы хоть что-то было написано.</article>

-------------------------

<a name="leh"></a>
## Лёха
![картинка](https://github.com/fplve/vika/raw/gh-pages/assets/images/sanya.png)
<article>Тут будет текст про этого человечка, я просто написал вот это, что бы хоть что-то было написано.</article>

-------------------------