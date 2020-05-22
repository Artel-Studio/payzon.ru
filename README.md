# payzon.ru
Интеграция по API с payzon.ru

<h2>Процесс оплаты</h2>

1. Создание ссылки на оплату и перенаправление на платёжный шлюз.
2. Осуществление платежа.
3. Получение уведомления на result_url.
4. Перенаправление клиента на success_url, либо на fail_url. (Этот шаг не обязателен.)

<h2>Создание ссылки на оплату</h2>

<h3>Простейший вариант</h3>

Вариант без дополнительных полей.

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

Вариант с дополнительными полями.

```php
// Обязательные поля
$api_key = '389b13b4b5e75ddc1f5ae50fd41f685f'; // Shop ID
$secret1 = 'LKFpeejWE5R5GJKJLjdldsjLGKGJLKjl'; // Secret1
$inv_id = 0; // Номер транзакции в системе вашего магаина. Если номера у вас не учитываются, ставьте 0
$amount = 500.10; // Сумма в рублях. Integer, либо Float. Разделитель дробной части — точка.
$descr = rawurlencode('Торт "Фантазия"');

$shop_user_id = $this->session->userdata('user_id');
// Контрольная сумма
$crc = md5("{$api_key}:{$inv_id}:{$amount}:{$secret1}:{$shop_user_id}");

// Перенаправляем на платёжный шлюз
header('Location: https://payzon.ru/checkout?'.
				'shop='.$api_key.'&'.
				'invoice='.(int)$inv_id.'&'.
				'amount='.$amount.'&'.
				'description='.$descr.'&'.
				'shop_user_id='.$shop_user_id.'&'.
				'signature='.$crc);
```
