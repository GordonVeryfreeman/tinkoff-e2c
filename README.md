# Python-обертка для интеграции с Tinkoff E2C

Реализует базовые операции, необходимые для работы с денежными переводами клиентов

## Установка и настройка CryptoPro для E2C Тинькофф банка

Исходные данные: сертификат %CERTIFICATE%.cer, папка с закрытым ключом %PRIVATE_KEY%

### 1 Скачиваем
Ссылка: https://www.cryptopro.ru/products/csp/downloads

Необходима предварительная регистрация или авторизация

### 2 Устанавливаем
Распаковываем скачанный архив: `tar -xf %ARCHIVE%`

Переходим в директорию с распакованными файлами: `cd %DIR%`

Запускаем установку: `sudo ./install.sh`

**Для дальнейшей работы переходим в директорию с утилитами:** (обычно `/opt/cprocsp/bin/amd64`, но может отличаться в зависимости от архитектуры)

### 3 Ставим лицензию
Устанавливаем лицензию: `sudo ./cpconfig -license -set %KEY%`

Проверяем лицензию: `./cpconfig -license -view`

**Дальнейшие команды исполняем от имени того пользователя, который будет работать с шифрованием** (обычно `www-data`, но может отличаться)

### 4 Устанавливаем контейнер
Проверяем, есть ли директория с ключами пользователя: `/var/opt/cprocsp/keys/www-data`

Если ее нет, то создаем и устанавливаем необходимые права: `sudo -u www-data mkdir -m 0700 /var/opt/cprocsp/keys/www-data`

Далее копируем папку с закрытым ключом в директорию с ключами пользователя: `sudo -u www-data cp %PRIVATE_KEY% /var/opt/cprocsp/keys/www-data`

Устанавливаем необходимые права на директорию: `sudo -u www-data chmod 0700 /var/opt/cprocsp/keys/www-data/%PRIVATE_KEY%`

И на сам ключ: `sudo -u www-data chmod 0600 /var/opt/cprocsp/keys/www-data/%PRIVATE_KEY%/*.key`

Проверяем наличие контейнера: `sudo -u www-data ./csptest -keyset -enum_cont -fqcn -verifyc`

Сохраняем название контейнера (подставить вместо %CONTAINER%), должно быть типа `\\.\HDIMAGE\xx-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`

Также можно посмотреть всю информацию о контейнере: `sudo -u www-data ./csptestf -keyset -container '%CONTAINER%' -info`

### 5 Привязываем сертификат
Ассоциируем сертификат с установленным контейнером: `sudo -u www-data ./certmgr -inst -file %CERTIFICATE%.cer -store uMy -cont '%CONTAINER%'`

Проверяем сертификат: `sudo -u www-data ./certmgr -list -store uMy`

**Если необходимо, то проделать операции 4-5 для другого пользователя, например, от имени которого запускается unit testing**

### Ссылки
- https://www.altlinux.org/КриптоПро - настройка под ALT Linux (годится под Ubuntu; не нужно устанавливать дополнительные пакеты)
- https://www.cryptopro.ru/forum2/default.aspx?g=posts&t=11300 - настройка под Ubuntu (для версии 4.0, но годится; не нужно устанавливать дополнительные пакеты)
