---
layout: default
---

## Что vk-бот из себя представляет.

> Чат-бот – это программа-собеседник имитирующая человеческое общение при помощи текста или голоса.
![бот](https://github.com/fplve/vika/raw/gh-pages/assets/images/listia_kirpichnaia_stena_rastenie_122951_1920x1080.jpg)
> Чат-боты помогают автоматизировать задачи, работая по заданному алгоритму. Они ведут диалог с пользователем, выполняя его просьбы, отвечая на запросы или развлекая своими ответами.
> 
> Одним из первых виртуальных собеседников была программа Элиза, созданная в 1966 году Джозефом Вейзенбаумом. Элиза пародировала речевое поведение психотерапевта, реализуя технику активного слушания, переспрашивая пользователя и используя фразы типа «Пожалуйста, продолжайте».

### Модуль cmdlist.php

Данный модуль выводит список $text.
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
### Модуль info.php

Данный модуль показывает список всех команд, доступных боту.
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