---
title: Настройка пользовательского linux-контейнера
description: Узнайте, как настроить пользовательский контейнер Linux в службе приложений Azure. В этой статье показаны наиболее распространенные задачи настройки.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280148"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Настройте пользовательский контейнер Linux для службы приложений Azure

В этой статье показано, как настроить пользовательский контейнер Linux для работы в службе приложений Azure.

В этом руководстве содержатся ключевые концепции и инструкции по контейнеризации приложений Linux в App Service. Если вы никогда не пользовались службой приложений Azure, сначала следуйте примеру запуска и [учебника](tutorial-custom-docker-image.md) [по специальному контейнеру.](quickstart-docker-go.md) Там также [несколько контейнеров приложение быстрый запуск](quickstart-multi-container.md) и [учебник](tutorial-multi-container-app.md).

## <a name="configure-port-number"></a>Настройка номера порта

Веб-сервер на пользовательском изображении может использовать порт, не превышающей 80. Вы сообщаете Azure о порту, который `WEBSITES_PORT` использует пользовательский контейнер с помощью настройки приложения. На странице GitHub с [примером кода Python в этом руководстве](https://github.com/Azure-Samples/docker-django-webapp-linux) показано, что для параметра `WEBSITES_PORT` необходимо задать значение _8000_. Вы можете установить [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) его, запустив команду в облачной оболочке. Пример:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Настройка переменных среды

Пользовательский контейнер может использовать переменные среды, которые должны быть поставлены извне. Вы можете передать их, запустив [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) команду в облачной оболочке. Пример:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Этот метод работает как для одноконтейнерных приложений, так и для приложений с несколькими контейнерами, где переменные среды указаны в файле *docker-compose.yml.*

## <a name="use-persistent-shared-storage"></a>Используйте постоянное совместное хранилище

Вы можете использовать *каталог /домашний* в файловой системе приложения, чтобы сохранять файлы при перезагрузках и делиться ими в разных экземплярах. Приложение `/home` предоставляется для того, чтобы приложение контейнера было доступно для постоянного хранения.

При отключении постоянного хранилища `/home` записи в каталог не сохраняются в перезапусках приложений или в нескольких экземплярах. Единственным исключением `/home/LogFiles` является каталог, который используется для хранения журналов Docker и контейнеров. При включении постоянного хранилища `/home` все записи в каталог сохраняются и могут быть доступны во всех экземплярах уменьшенного приложения.

По умолчанию постоянное хранилище *включено,* и настройка не разоблачается в настройках приложения. Чтобы отключить его, установите `WEBSITES_ENABLE_APP_SERVICE_STORAGE` настройку [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) приложения, запустив команду в облачной оболочке. Пример:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Вы также можете [настроить свой собственный постоянный хранения.](how-to-serve-content-from-azure-storage.md)

## <a name="enable-ssh"></a>Включение SSH

SSH обеспечивает безопасный обмен данными между клиентом и контейнером. Для того, чтобы пользовательский контейнер для поддержки SSH, вы должны добавить его в Dockerfile себя.

> [!TIP]
> Все встроенные контейнеры Linux добавили инструкции SSH в свои репозитории изображений. Вы можете пройти через следующие инструкции с [репозиторием Node.js 10.14,](https://github.com/Azure-App-Service/node/blob/master/10.14) чтобы увидеть, как он включен там.

- Используйте инструкцию [RUN](https://docs.docker.com/engine/reference/builder/#run) для установки сервера SSH `"Docker!"`и установить пароль для корневой учетной записи. Например, для изображения, основанного на [Альпийском Linux,](https://hub.docker.com/_/alpine)вам нужны следующие команды:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Эта конфигурация не позволяет внешние подключения к контейнеру. SSH доступен `https://<app-name>.scm.azurewebsites.net` только через и аутентифицируется с публикацией учетных данных.

- Добавьте [этот sshd_config файл](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) в репозиторий изображений и используйте инструкцию [COPY](https://docs.docker.com/engine/reference/builder/#copy) для копирования файла в *каталог /etc/ssh/.* Для получения дополнительной информации о *sshd_config* файлах, [OpenBSD documentation](https://man.openbsd.org/sshd_config)см.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Файл *sshd_config* должен содержать приведенные ниже элементы.
    > - `Ciphers` должен содержать по крайней мере один элемент в этом списке: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` должен содержать по крайней мере один элемент в этом списке: `hmac-sha1,hmac-sha1-96`.

- Используйте инструкцию [EXPOSE,](https://docs.docker.com/engine/reference/builder/#expose) чтобы открыть порт 2222 в контейнере. Хотя корневой пароль известен, порт 2222 недоступен из Интернета. Он доступен только контейнерами в сети моста частной виртуальной сети.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- В сценарии запуска контейнера запустите сервер SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Например, посмотрите, как [контейнер Node.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) по умолчанию запускает сервер SSH.

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Настройка многоконтейнерных приложений

- [Используйте постоянное хранилище в Docker Compose](#use-persistent-storage-in-docker-compose)
- [Ограничения предварительной версии](#preview-limitations)
- [Докер Компоге варианты](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Используйте постоянное хранилище в Docker Compose

Многоконтейнерные приложения, такие как WordPress, нуждаются в постоянном хранении для нормального функционирования. Чтобы включить его, конфигурация Docker Compose должна указывать на место хранения *за пределами* контейнера. Места хранения внутри контейнера не сохраняются, помимо перезагрузки приложения.

Включите постоянное `WEBSITES_ENABLE_APP_SERVICE_STORAGE` хранилище, установив настройку приложения, используя [настройки конфигураций веб-приложений az webapp](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) в облачной оболочке.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

В файле *docker-compose.yml* `volumes` отображается `${WEBAPP_STORAGE_HOME}`опция . 

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

Мультиконтейнер в настоящее время находится в предварительном просмотре. Следующие функции платформы App Service не поддерживаются:

- проверка подлинности/авторизация;
- управляемые удостоверения.

### <a name="docker-compose-options"></a>Докер Компоге варианты

Следующие списки показывают поддерживаемые и неподдерживаемые параметры конфигурации Docker Compose:

#### <a name="supported-options"></a>Поддерживаемые параметры

- command
- entrypoint;
- Среда
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
> Любые другие опции, явно не вызванные, игнорируются в публичном предварительном просмотре.

## <a name="configure-vnet-integration"></a>Настройка интеграции VNet

Использование пользовательского контейнера с интеграцией VNet может потребовать дополнительной конфигурации контейнера. См. статью [Интеграция приложения с виртуальной сетью Azure](../web-sites-integrate-with-vnet.md).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Учебник: Развертывание из частного контейнерного репозитория](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Учебник: Мультиконтейнер WordPress приложение](tutorial-multi-container-app.md)
