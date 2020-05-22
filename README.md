# payzon.ru
Интеграция по API с payzon.ru

<h2>Процесс оплаты</h2>

1. Создание ссылки на оплату и перенаправление на платёжный шлюз.
2. Осуществление платежа.
3. Получение уведомления на result_url.
4. Перенаправление клиента на success_url, либо на fail_url. (Этот шаг не обязателен.)

<h2>Создание ссылки на оплату</h2>

<h3>Простейший вариант (без дополнительных полей)</h3>

```php
// Обязательные поля
$api_key = '389b13b4b5e75ddc1f5ae50fd41f685f'; // Shop ID
$secret1 = 'LKFpeejWE5R5GJKJLjdldsjLGKGJLKjl'; // Secret1
$inv_id = 0; // Номер транзакции в системе вашего магаина. Если номера у вас не учитываются, ставьте 0
$amount = 500.10; // Сумма в рублях. Integer, либо Float. Разделитель дробной части — точка.
$descr = rawurlencode('Торт "Фантазия"');

// Контрольная сумма
$crc = md5("{$api_key}:{$inv_id}:{$amount}:{$secret1}");

// Перенаправляем на платёжный шлюз
header('Location: https://payzon.ru/checkout?'.
				'shop='.$api_key.'&'.
				'invoice='.(int)$inv_id.'&'.
				'amount='.$amount.'&'.
				'description='.$descr.'&'.
				'shop_user_id='.$shop_user_id.'&'.
				'signature='.$crc);
```

<h3>Вариант с дополнительными полями.</h3>

Есть несколько важных правил, которые следует учесть:

1. Имя любого дополнительного поля должно начинаться с ```shop_```

Пример: ```shop_article```

2. При формировании контрольной суммы дополнительные поля выстраиваются в алфавитном порядке.
3. В контрольную сумму вносятся только значения полей.
4. Все параметры в контрольной сумме разделяются знаком ```:``` (двоеточие).

$crc = md5("{$api_key}:{$inv_id}:{$amount}:{$secret1}:{$shop_abstract}:{$shop_article}:{$shop_boo}");

```php
// Обязательные поля
$api_key = '389b13b4b5e75ddc1f5ae50fd41f685f'; // Shop ID
$secret1 = 'LKFpeejWE5R5GJKJLjdldsjLGKGJLKjl'; // Secret1
$inv_id = 0; // Номер транзакции в системе вашего магаина. Если номера у вас не учитываются, ставьте 0
$amount = 500.10; // Сумма в рублях. Integer, либо Float. Разделитель дробной части — точка.
$descr = rawurlencode('Торт "Фантазия"');

// Дополнительные поля
$shop_abstract = 'Какие-то ваши данные';
$shop_article = '048645';
$shop_boo = 'Что там вам требуется';

// Контрольная сумма
$crc = md5("{$api_key}:{$inv_id}:{$amount}:{$secret1}:{$shop_abstract}:{$shop_article}:{$shop_boo}");

// Перенаправляем на платёжный шлюз
header('Location: https://payzon.ru/checkout?'.
				'shop='.$api_key.'&'.
				'invoice='.(int)$inv_id.'&'.
				'amount='.$amount.'&'.
				'description='.$descr.'&'.
				'shop_user_id='.$shop_user_id.'&'.
				'signature='.$crc);
```

<h2>Осуществление платежа</h2>

Платёж осуществляется на сайте payzon.ru, поэтому тут ничего делать не требуется.

<h2>Получение уведомления на result_url</h2>

Если платёж прошёл успешно, вам на секретный result_url, который вы указали в настройках магазина, придёт POST запрос со всей необходимой информацией, подписанный секретным ключом Secret2. В ответ на этот запрос необходимо вернуть сообщение ```OK```

