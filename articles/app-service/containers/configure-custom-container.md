---
title: Настройка клиента контейнеры — службы приложений Azure | Документация Майкрософт
description: Сведения о настройке приложения Node.js на работу в службе приложений Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 1e5faa8d356b891d825586414c0a1a1b9fa47090
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001887"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Настройка пользовательского контейнера Linux для службы приложений Azure

В этой статье показано, как настроить пользовательский контейнер Linux для запуска в службе приложений Azure.

Это руководство содержит основные понятия и инструкции по контейнеризации приложений Linux в службе приложений. Если вы раньше не использовали службы приложений Azure, выполните [быстрого запуска пользовательского контейнера](quickstart-docker-go.md) и [руководстве](tutorial-custom-docker-image.md) первого. Имеется также [быстрого запуска многоконтейнерного приложения](quickstart-multi-container.md) и [руководстве](tutorial-multi-container-app.md).

## <a name="configure-port-number"></a>Номера портов

Веб-сервера в пользовательский образ может использовать порт, отличный от 80. Сообщить Azure, порт, с помощью пользовательских `WEBSITES_PORT` параметр приложения. На странице GitHub с [примером кода Python в этом руководстве](https://github.com/Azure-Samples/docker-django-webapp-linux) показано, что для параметра `WEBSITES_PORT` необходимо задать значение _8000_. Можно разместить его, выполнив [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) команду в Cloud Shell. Например: 

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Настройка переменных среды

Ваш пользовательский контейнер может использовать переменные среды, которые необходимо представить извне. Можно передать их в, запустив [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) команду в Cloud Shell. Например: 

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Этот метод работает для приложений в одном контейнере или многоконтейнерных приложений, где заданы переменные среды в *docker-compose.yml* файл.

## <a name="use-persistent-shared-storage"></a>Использовать постоянное хранилище общего доступа

Можно использовать */home* каталог в файловой системе приложения для сохранения файлов между перезагрузками и совместно использовать их в экземплярах. `/home` В приложении предоставляется для обеспечения доступа к постоянному хранилищу вашего приложения-контейнера.

При постоянном хранилище отключено, а затем записывает `/home` каталога не сохраняются между перезагрузками приложения или на нескольких экземплярах. Единственным исключением является `/home/LogFiles` каталог, который используется для хранения журналов Docker и контейнерах. При включении постоянное хранилище, все операции записи `/home` directory сохраняются и доступны для всех экземпляров горизонтально масштабируемого приложения.

По умолчанию является постоянное хранилище *отключена*. Чтобы включить или отключить его, установите `WEBSITES_ENABLE_APP_SERVICE_STORAGE` параметр приложения, выполнив [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) команду в Cloud Shell. Например: 

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

> [!NOTE]
> Вы также можете [настроить собственные постоянное хранилище](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Включение SSH

SSH обеспечивает безопасный обмен данными между клиентом и контейнером. Чтобы пользовательский контейнер для поддержки SSH необходимо добавить в него самого.

> [!TIP]
> Все встроенные контейнеры Linux добавили инструкции SSH в репозиториях образа. Можно выполнить следующие инструкции с [репозитории Node.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14) чтобы увидеть, как он включен существует.

- Используйте [ЗАПУСКА](https://docs.docker.com/engine/reference/builder/#run) инструкции для установки SSH-сервер и задать пароль для учетной записи root для `"Docker!"`. Например, для образа на основе [Alpine Linux](https://hub.docker.com/_/alpine), вам потребуется следующие команды:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Эта конфигурация не разрешает внешние подключения к контейнеру. SSH доступен только через `https://<app-name>.scm.azurewebsites.net` и проверку подлинности с учетными данными для публикации.

- Добавить [этот файл sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) в свой репозиторий образов и использовать [КОПИРОВАНИЯ](https://docs.docker.com/engine/reference/builder/#copy) инструкции для копирования файла в */etc/ssh/* каталога. Дополнительные сведения о *sshd_config* файлы, см. в разделе [OpenBSD документации](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Файл *sshd_config* должен содержать приведенные ниже элементы.
    > - `Ciphers` должен содержать по крайней мере один элемент в этом списке: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` должен содержать по крайней мере один элемент в этом списке: `hmac-sha1,hmac-sha1-96`.

- Используйте [ПРЕДОСТАВЛЯТЬ](https://docs.docker.com/engine/reference/builder/#expose) инструкции, чтобы открыть порт 2222 в контейнере. Несмотря на то, что известен пароль пользователя root, порт 2222 недоступен из Интернета. Он доступен контейнерам внутри Мостовой сети виртуальной частной сети.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- В сценарии запуска для вашего контейнера запустите сервер SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Например, см. в разделе как значение по умолчанию [Node.js 10.14 контейнера](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) запускает сервер SSH.

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Настройка приложений с несколькими контейнерами

- [Использовать постоянное хранилище в Docker Compose](#use-persistent-storage-in-docker-compose)
- [Ограничения предварительной версии](#preview-limitations)
- [Параметры docker Compose](#docker-compose-options)
- [Параметры конфигурации Kubernetes](#kubernetes-configuration-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Использовать постоянное хранилище в Docker Compose

Многоконтейнерных приложений, такие как WordPress требуется постоянное хранилище для правильной. Чтобы включить ее, конфигурацию Docker Compose должен указывать на расположение хранилища *за пределами* контейнера. Места хранения внутри контейнера не сохранять изменения, помимо перезапуск приложения.

Включить постоянное хранилище, задав `WEBSITES_ENABLE_APP_SERVICE_STORAGE` приложения с помощью [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) команду в Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

В вашей *docker-compose.yml* файл "," Карта `volumes` равным `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME` — это переменная среды в Службе приложений, которая сопоставляется с постоянным хранилищем для вашего приложения. Например: 

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="use-custom-storage-in-docker-compose"></a>Использование пользовательского хранилища в Docker Compose

Хранилище Azure (файлы Azure или BLOB-объектов Azure) можно подключить с многоконтейнерных приложений с помощью пользовательских id. Чтобы просмотреть имя custom-id, выполните [ `az webapp config storage-account list --name <app_name> --resource-group <resource_group>` ](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

В вашей *docker-compose.yml* файл "," Карта `volumes` равным `custom-id`. Например: 

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

### <a name="preview-limitations"></a>Ограничения предварительной версии

Несколькими контейнерами в настоящее время доступна Предварительная версия. Следующие функции платформы службы приложений не поддерживаются:

- проверка подлинности/авторизация;
- управляемые удостоверения.

### <a name="docker-compose-options"></a>Параметры docker Compose

В следующих списках приведены поддерживаемые и неподдерживаемые Docker Compose параметры конфигурации:

#### <a name="supported-options"></a>Поддерживаемые параметры

- command
- entrypoint;
- Среда
- image
- ports;
- restart
- services;
- volumes.

#### <a name="unsupported-options"></a>Неподдерживаемые параметры

- build (недопустимо);
- depends_on (игнорируется);
- networks (игнорируется);
- secrets (игнорируется);
- порты, отличные от 80 и 8080 (игнорируются).

> [!NOTE]
> В общедоступной предварительной версии учитываются другие параметры явно не указано.

### <a name="kubernetes-configuration-options"></a>Параметры конфигурации Kubernetes

Для Kubernetes поддерживаются следующие параметры конфигурации:

- args
- command
- containers
- image
- name
- ports;
- spec.

> [!NOTE]
> Другие параметры явно не указано не поддерживаются в общедоступной предварительной версии.
>

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство по развертыванию из частного репозитория контейнеров](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Руководство по приложению WordPress с несколькими контейнерами](tutorial-multi-container-app.md)
