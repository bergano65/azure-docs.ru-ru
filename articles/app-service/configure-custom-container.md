---
title: Настройка пользовательского контейнера Linux
description: Узнайте, как настроить пользовательский контейнер Linux в службе приложений Azure. В этой статье показаны наиболее распространенные задачи настройки.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9a27abe5457cf8adf2963db545c629134ae53709
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566982"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Настройка пользовательского контейнера Linux для службы приложений Azure

В этой статье показано, как настроить пользовательский контейнер Linux для запуска в службе приложений Azure.

Это краткое описание содержит основные понятия и инструкции по работе с контейнерами приложений Linux в службе приложений. Если вы никогда не использовали службу приложений Azure, сначала ознакомьтесь с кратким [руководством](tutorial-custom-container.md?pivots=container-linux) по [пользовательскому контейнеру](quickstart-custom-container.md?pivots=container-linux) и руководству. Существует также краткое [руководство](tutorial-multi-container-app.md)по [многоконтейнерному приложению](quickstart-multi-container.md) .

## <a name="configure-port-number"></a>Настройка номера порта

По умолчанию служба приложений предполагает, что ваш пользовательский контейнер прослушивает порт 80. Веб-сервер в пользовательском образе может использовать порт, отличный от 80. Вы сообщаете Azure о порте, который использует настраиваемый контейнер, с помощью `WEBSITES_PORT` параметра приложения. На странице GitHub с [примером кода Python в этом руководстве](https://github.com/Azure-Samples/docker-django-webapp-linux) показано, что для параметра `WEBSITES_PORT` необходимо задать значение _8000_. Его можно задать, выполнив [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) команду в Cloud Shell. Пример:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Настройка переменных среды

Пользовательский контейнер может использовать переменные среды, которые необходимо предоставить извне. Их можно передать в, выполнив [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) команду в Cloud Shell. Пример:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Этот метод работает как для приложений с одним контейнером, так и для приложений с несколькими контейнерами, где переменные среды указаны в файле *DOCKER-Compose. yml* .

## <a name="use-persistent-shared-storage"></a>Использовать постоянное общее хранилище

Вы можете использовать каталог */Home* в файловой системе приложения для сохранения файлов между перезапусками и их совместного использования в экземплярах. В `/home` приложении предоставляется разрешение на доступ к постоянному хранилищу в приложении-контейнере.

Если постоянное хранилище отключено, записи в `/home` каталог не сохраняются при перезапуске приложения или в нескольких экземплярах. Единственным исключением является `/home/LogFiles` каталог, который используется для хранения журналов DOCKER и контейнеров. Если постоянное хранилище включено, все операции записи в `/home` Каталог сохраняются и доступны для всех экземпляров масштабируемого приложения.

По умолчанию постоянное хранилище *включено* и параметр не отображается в параметрах приложения. Чтобы отключить его, задайте `WEBSITES_ENABLE_APP_SERVICE_STORAGE` параметр приложения, выполнив [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) команду в Cloud Shell. Пример:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Вы также можете [настроить постоянное хранилище](configure-connect-to-azure-storage.md?pivots=platform-linux).

## <a name="enable-ssh"></a>Включение SSH

SSH обеспечивает безопасный обмен данными между клиентом и контейнером. Чтобы пользовательский контейнер поддерживал SSH, его необходимо добавить в Dockerfile.

> [!TIP]
> Все встроенные контейнеры Linux добавили инструкции SSH в свои репозитории образов. Чтобы увидеть, как он включен, можно выполнить приведенные ниже инструкции с [ репозиториемNode.js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14) .

- Используйте инструкцию [Run](https://docs.docker.com/engine/reference/builder/#run) для установки сервера SSH и задайте для пароля учетной записи root значение `"Docker!"` . Например, для образа, основанного на [Alpine Linux](https://hub.docker.com/_/alpine), требуются следующие команды:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Эта конфигурация не допускает внешние подключения к контейнеру. SSH доступен только через `https://<app-name>.scm.azurewebsites.net` учетные данные публикации и проходит проверку подлинности.

- Добавьте [этот файл sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) в репозиторий образов и скопируйте файл в каталог *каталог/etc/SSH/* с помощью инструкции [Copy](https://docs.docker.com/engine/reference/builder/#copy) . Дополнительные сведения о *sshd_config* файлах см. в [документации по OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Файл *sshd_config* должен содержать приведенные ниже элементы.
    > - `Ciphers` должен содержать по крайней мере один элемент в этом списке: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` должен содержать по крайней мере один элемент в этом списке: `hmac-sha1,hmac-sha1-96`.

- Используйте инструкцию [предоставления](https://docs.docker.com/engine/reference/builder/#expose) , чтобы открыть порт 2222 в контейнере. Несмотря на то, что пароль root известен, порт 2222 недоступен из Интернета. Он доступен только контейнерам в сети моста частной виртуальной сети.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- В скрипте запуска для контейнера запустите SSH-сервер.

    ```bash
    /usr/sbin/sshd
    ```

    Пример см. в разделе как [ контейнерNode.js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) по умолчанию запускает сервер SSH.

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="change-the-docker-image-of-a-custom-container"></a>Изменение образа DOCKER пользовательского контейнера

Чтобы изменить существующее приложение-контейнер из текущего образа DOCKER на новый образ, используйте следующую команду:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Использование образа из частного реестра

Чтобы использовать образ из частного реестра, например реестра контейнеров Azure, выполните следующую команду:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

Для *\<username>* и *\<password>* укажите учетные данные для входа в закрытую учетную запись реестра.

## <a name="configure-multi-container-apps"></a>Настройка приложений с несколькими контейнерами

- [Использование постоянного хранилища в Docker Compose](#use-persistent-storage-in-docker-compose)
- [Ограничения предварительной версии](#preview-limitations)
- [Параметры Docker Compose](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Использование постоянного хранилища в Docker Compose

Для правильной работы приложений с несколькими контейнерами, таких как WordPress, требуется постоянное хранилище. Чтобы включить его, конфигурация Docker Compose должна указывать на место хранения *за пределами* контейнера. Расположения хранилищ внутри контейнера не сохраняют изменения после перезапуска приложения.

Включите постоянное хранилище, задав `WEBSITES_ENABLE_APP_SERVICE_STORAGE` параметр приложения с помощью команды [AZ webapp config appSettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) в Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

В файле *DOCKER-Compose. yml* сопоставьте `volumes` параметр с `${WEBAPP_STORAGE_HOME}` . 

`WEBAPP_STORAGE_HOME` — это переменная среды в Службе приложений, которая сопоставляется с постоянным хранилищем для вашего приложения. Пример:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Ограничения предварительной версии

В настоящее время поддерживается Предварительная версия нескольких контейнеров. Следующие функции платформы службы приложений не поддерживаются:

- проверка подлинности/авторизация;
- управляемые удостоверения.
- CORS

### <a name="docker-compose-options"></a>Параметры Docker Compose

В следующих списках показаны поддерживаемые и неподдерживаемые параметры конфигурации Docker Compose.

#### <a name="supported-options"></a>Поддерживаемые варианты

- .
- entrypoint;
- среда
- Изображение
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
> Любые другие параметры, не вызываемые явно, игнорируются в общедоступной предварительной версии.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. по развертыванию из частного репозитория контейнеров](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [Руководство. по приложению WordPress с несколькими контейнерами](tutorial-multi-container-app.md)
