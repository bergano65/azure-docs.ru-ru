---
title: Настройка пользовательского контейнера Linux
description: Узнайте, как настроить пользовательский контейнер Linux в службе приложений Azure. В этой статье приведены наиболее распространенные задачи настройки.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: d9d6311e69ba4e3893da81a16b06c8baed78cdcd
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671865"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Настройка пользовательского контейнера Linux для службы приложений Azure

В этой статье показано, как настроить пользовательский контейнер Linux для запуска в службе приложений Azure.

Это краткое описание содержит основные понятия и инструкции по работе с контейнерами приложений Linux в службе приложений. Если вы никогда не использовали службу приложений Azure, сначала ознакомьтесь с кратким [руководством](tutorial-custom-docker-image.md) по [пользовательскому контейнеру](quickstart-docker-go.md) и руководству. Существует также краткое [руководство](tutorial-multi-container-app.md)по [многоконтейнерному приложению](quickstart-multi-container.md) .

## <a name="configure-port-number"></a>Настройка номера порта

Веб-сервер в пользовательском образе может использовать порт, отличный от 80. Вы сообщаете Azure о порте, который использует настраиваемый контейнер, с помощью параметра приложения `WEBSITES_PORT`. На странице GitHub с [примером кода Python в этом руководстве](https://github.com/Azure-Samples/docker-django-webapp-linux) показано, что для параметра `WEBSITES_PORT` необходимо задать значение _8000_. Его можно задать, выполнив команду [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) в Cloud Shell. Пример.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Настройка переменных среды

Пользовательский контейнер может использовать переменные среды, которые необходимо предоставить извне. Их можно передать в, выполнив команду [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) в Cloud Shell. Пример.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Этот метод работает как для приложений с одним контейнером, так и для приложений с несколькими контейнерами, где переменные среды указаны в файле *DOCKER-Compose. yml* .

## <a name="use-persistent-shared-storage"></a>Использовать постоянное общее хранилище

Вы можете использовать каталог */Home* в файловой системе приложения для сохранения файлов между перезапусками и их совместного использования в экземплярах. В приложении предоставлены `/home`, позволяющие приложению-контейнеру получать доступ к постоянному хранилищу.

Если постоянное хранилище отключено, запись в каталог `/home` не сохраняется между перезапусками приложений или несколькими экземплярами. Единственным исключением является каталог `/home/LogFiles`, который используется для хранения журналов DOCKER и контейнеров. Если постоянное хранилище включено, все операции записи в каталог `/home` сохраняются и доступны для всех экземпляров масштабируемого приложения.

По умолчанию постоянное хранилище *включено* и параметр не отображается в параметрах приложения. Чтобы отключить его, задайте параметр приложения `WEBSITES_ENABLE_APP_SERVICE_STORAGE`, выполнив команду [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) в Cloud Shell. Пример.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Вы также можете [настроить постоянное хранилище](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Включение SSH

SSH обеспечивает безопасный обмен данными между клиентом и контейнером. Чтобы пользовательский контейнер поддерживал SSH, его необходимо добавить в Dockerfile.

> [!TIP]
> Все встроенные контейнеры Linux добавили инструкции SSH в свои репозитории образов. Чтобы увидеть, как он включен, можно выполнить приведенные ниже инструкции с помощью [репозитория Node. js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14) .

- Используйте инструкцию [Run](https://docs.docker.com/engine/reference/builder/#run) для установки сервера SSH и задайте для учетной записи root значение `"Docker!"`. Например, для образа, основанного на [Alpine Linux](https://hub.docker.com/_/alpine), требуются следующие команды:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Эта конфигурация не разрешает внешние подключения к контейнеру. Протокол SSH доступен только для `https://<app-name>.scm.azurewebsites.net` и аутентификации с учетными данными публикации.

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

    Пример см. в разделе как [контейнер Node. js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) по умолчанию запускает сервер SSH.

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Настройка приложений с несколькими контейнерами

- [Использование постоянного хранилища в Docker Compose](#use-persistent-storage-in-docker-compose)
- [Ограничения предварительной версии](#preview-limitations)
- [Параметры Docker Compose](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Использование постоянного хранилища в Docker Compose

Для правильной работы приложений с несколькими контейнерами, таких как WordPress, требуется постоянное хранилище. Чтобы включить его, конфигурация Docker Compose должна указывать на место хранения *за пределами* контейнера. Расположения хранилищ внутри контейнера не сохраняют изменения после перезапуска приложения.

Включите постоянное хранилище, задав параметр приложения `WEBSITES_ENABLE_APP_SERVICE_STORAGE`, используя команду [AZ webapp config appSettings Set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) в Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

В файле *DOCKER-Compose. yml* сопоставьте параметр `volumes` для `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME` — это переменная среды в Службе приложений, которая сопоставляется с постоянным хранилищем для вашего приложения. Пример.

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

### <a name="docker-compose-options"></a>Параметры Docker Compose

В следующих списках показаны поддерживаемые и неподдерживаемые параметры конфигурации Docker Compose.

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
> Любые другие параметры, не вызываемые явно, игнорируются в общедоступной предварительной версии.

## <a name="configure-vnet-integration"></a>Настройка интеграции с виртуальной сетью

Для использования пользовательского контейнера с интеграцией виртуальной сети может потребоваться дополнительная настройка контейнера. См. статью [Интеграция приложения с виртуальной сетью Azure](../web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. Развертывание из закрытого репозитория контейнеров](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Учебник. многоконтейнерное приложение WordPress](tutorial-multi-container-app.md)
