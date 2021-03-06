# Авторизация
Первый раздел Sudox, содержащий в себе **4 экрана**. Данный раздел используется для идентификации нового и старого *Пользователя*, переноса сообщений *Пользователя*.

### Номера кодов результатов


**0101** - **AUTH_NOT_FOUND** - авторизация не найдена

**0102** - **AUTH_EXISTS** - авторизация существует

**0103** - **AUTH_DROPPED** - истек срок жизни авторизации

**0104** - **AUTH_TYPE_INVALID** - невозможно выполнить метод на этом типе авторизации

**0105** - **AUTH_CODE_INVALID** - неправильный проверочный код

**0106** - **AUTH_CODE_SENT** - код был отправлен

**0107** - **USER_PHONE_BANNED** - телефон пользователя забанен

**0108** - **USER_KEY_HASH_INVALID** - неверный хэш пользовательского ключа


### Тайм-ауты
**Ошибка 0102** - AUTH_DROPPED = 10 минут

### Стек технологий
1. [LibSodium](https://libsodium.gitbook.io/) - библиотека шифрования


### Экраны
1. Экран ввода номера телефона.
2. Экран ввода проверочного кода из SMS.
3. Экран регистрационных данных.
4. Экран входа по *Cloud End-To-End*.

### Моменты вызова
1. После установки *Приложения*.
2. После выхода из аккаунта *Приложения*.


### Карта связей экранов и описание запросов
Схема, описывающая алгоритм отправки запросов, получения ответов, переходов между фрагментам.

![Авторизация](/images/auth_sheme.jpg)

Далее идёт описание схемы (см. **рисунок 1.1**)

Во всей авторизации используется сервис ***auth***

## Инициализация процесса регистрации или авторизации
1. Отправка номер телефона на сервер.
2. Проверка номера телефона и отправка результата выполнения, типа регистрации и токен сессии авторизации.
id сессии авторизации - особая строка, позволяющая быстро возобновить авторизацию *Пользователя* после обрыва соединения или тайм-аута.

[Запрос auth.create](requests/create.md)

Далее в зависимости от значения поле `<bool> registered` идет решение о старте либо регистрации, либо авторизации.

## Регистрация
3. Отправка кода подтверждения на сервер
4. Проверка кода и отправка результата выполнения запроса на клиент
5. Отправка никнейма на сервер
6. Проверка данных и отправка результата выполнения запроса на клиент

3-4 [Запрос auth.checkCode](requests/check_code.md)

5-6 [Запрос auth.signUp](requests/sign_up.md)

## Авторизация
3. Отправка публичного ключа на сервер.
4. Получение публичного ключа от второго устройства *Пользователя* и зашифрованного пользовательского ключа.
5. ***Авторизация через приватный ключ.*** Отправка хэша **BLAKE2b** пользовательского ключа и ранее полученного токена авторизации. 
6. Получение токена для создания *Липкой сессии* *Пользователя* (см. [Главный экран]()).
7. Запрос клиента на отправку SMS на телефон *Пользователя* с кодом подтверждения.
8. Получение от сервера результата отправки кода
9. ***Авторизация через код.*** Отправка введенного пользователем кода на сервер
10. Проверка кода и отправка результата выполнения запроса на клиент.

[Запрос auth.verify](requests/verify.md)

[Запрос auth.respondVerify](requests/accept_verify.md)

[Запрос auth.signIn](requests/sign_in.md)

[Запрос auth.sendCode](requests/send_code.md)

[Запрос auth.signInForce](requests/sign_in_force.md)