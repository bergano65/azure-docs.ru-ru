---
title: Руководство по сборке и запуску пользовательского образа
description: Сведения о создании пользовательского образа Linux, который можно запустить в Службе приложений Azure, развернуть в Реестры контейнеров Azure и запустить в Службе приложений.
keywords: служба приложений azure, веб-приложение, docker, контейнер
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d960af01eed9fae0fec2566772799e4972053d7b
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687502"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Руководство по созданию и запуску настраиваемого образа в Службе приложений из частного реестра

[Служба приложений](app-service-linux-intro.md) предоставляет встроенные образы Docker на основе Linux с поддержкой определенных версий, включая PHP 7.3 и Node.js 10.14. Служба приложений использует технологию контейнеров Docker для размещения встроенных и настраиваемых образов в качестве платформы как услуги. В этом руководстве вы узнаете, как создать настраиваемый образ и запустить его в Службе приложений. Этот шаблон используется, если встроенные образы не содержат нужный язык или для приложения требуется определенная конфигурация, которую не предоставляют встроенные образы.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Развертывание настраиваемого образа в частном реестре контейнеров.
> * Запуск настраиваемого образа в Службе приложений.
> * Настройка переменных среды
> * Обновление и повторное развертывание образа.
> * Доступ к журналам диагностики
> * Подключение контейнера с помощью SSH.

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником необходимы указанные ниже компоненты.

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)

## <a name="download-the-sample"></a>Скачивание примера приложения

В окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения на локальный компьютер, а затем перейдите в каталог, в котором содержится образец кода.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Создание образа на основе файла Docker

В репозитории Git просмотрите файл _Dockerfile_. Этот файл описывает среду Python, необходимую для запуска приложения. Образ также настраивает сервер [SSH](https://www.ssh.com/ssh/protocol/) для обеспечения безопасного взаимодействия между контейнером и узлом.

```Dockerfile
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Создайте образ Docker командой `docker build`.

```bash
docker build --tag mydockerimage .
```

Проверьте, работает ли сборка, запустив контейнер Docker. Выполните команду [​​`docker run`](https://docs.docker.com/engine/reference/commandline/run/), указав имя и тег образа. Обязательно укажите порт с помощью аргумента `-p`.

```bash
docker run -p 8000:8000 mydockerimage
```

Убедитесь, что веб-приложение и контейнер функционируют правильно, перейдя по адресу `http://localhost:8000`.

![Локальное тестирование веб-приложения](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Развертывание приложения в Azure

Чтобы создать приложение, использующее образ, который вы только что создали, выполните команды Azure CLI, которые создадут группу ресурсов, отправят образ и затем создадут веб-приложение плана Службы приложений для его запуска.

### <a name="create-a-resource-group"></a>Создание группы ресурсов

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Создание реестра контейнеров Azure

В Cloud Shell создайте реестр контейнеров Azure с помощью команды [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create).

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Вход в Реестр контейнеров Azure

Чтобы отправить образ в реестр, нужно выполнить аутентификацию в частном реестре. В Cloud Shell используйте команду [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show), чтобы получить учетные данные из реестра, который вы создали.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

Выходные данные этой команды содержат два пароля и имя пользователя.

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<registry-username>"
}
```

В окне терминала на локальном компьютере войдите в Реестр контейнеров Azure с помощью команды `docker login`, как показано в примере ниже. Замените *\<azure-container-registry-name>* и *\<registry-username>* значениями для своего реестра. При появлении запроса введите один из паролей из предыдущего шага.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Убедитесь, что вход выполнен успешно.

### <a name="push-image-to-azure-container-registry"></a>Передача образа в реестр контейнеров Azure

Пометьте локальный образ тегом для Реестра контейнеров Azure. Например:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Отправьте образ с помощью команды `docker push`. Присвойте образу имя реестра, за которым следует имя и тег образа.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

В Cloud Shell убедитесь, что отправка образа выполнена успешно.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Вы должны увидеть следующий результат.

```json
[
  "mydockerimage"
]
```

### <a name="create-app-service-plan"></a>Создание плана службы приложений

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Создание веб-приложения

В Cloud Shell создайте [веб-приложение](app-service-linux-intro.md) в рамках плана службы приложений `myAppServicePlan` с помощью команды [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Замените _\<app-name>_ уникальным именем приложения, а _\<azure-container-registry-name>_ — именем реестра.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Когда веб-приложение будет создано, в Azure CLI отобразится примерно следующее:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-registry-credentials-in-web-app"></a>Настройка учетных данных реестра в веб-приложении

Чтобы Служба приложений могла извлечь частный образ, ей требуются сведения о реестре и образе. Предоставьте их, выполнив в Cloud Shell команду [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Замените значения *\<app-name>* , *\<azure-container-registry-name>* , _\<registry-username>_ и _\<password>_ .

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> При использовании реестра, отличного от Docker Hub, `--docker-registry-server-url` должен содержать `https://` и полное доменное имя реестра.
>

### <a name="configure-environment-variables"></a>Настройка переменных среды

В большинстве образов Docker используются настраиваемые переменные среды, например порт, отличный от 80. Чтобы сообщить Службе приложений, какой порт использует образ, используется параметр приложения `WEBSITES_PORT`. На странице GitHub с [примером кода Python в этом руководстве](https://github.com/Azure-Samples/docker-django-webapp-linux) показано, что для параметра `WEBSITES_PORT` необходимо задать значение _8000_.

Чтобы задать параметры приложения, выполните команду [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) в Cloud Shell. Параметры приложения чувствительны к регистру и используются с разделителями-пробелами.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>Тестирование веб-приложения

Убедитесь, что веб-приложение работает, открыв его (`http://<app-name>.azurewebsites.net`).

> [!NOTE]
> Первое обращение к приложению может занять некоторое время, так как Службе приложений нужно извлечь весь образ. Если время ожидания браузера истечет, просто обновите страницу.

![Тестирование настройки порта веб-приложения](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Изменение и повторное развертывание веб-приложения

Откройте файл app/templates/app/index.html в локальном репозитории Git. Найдите первый элемент HTML и измените его.

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
      </div>
    </div>
  </nav>
```

После того как вы изменили и сохранили файл Python, необходимо перестроить и передать новый образ Docker. Затем перезапустите веб-приложение, чтобы изменения вступили в силу. Используйте те же команды, которые использовались ранее в этом руководстве. Вы можете ознакомиться с разделами [Создание образа на основе файла Docker](#build-the-image-from-the-docker-file) и [Передача образа в реестр контейнеров Azure](#push-image-to-azure-container-registry). [Протестируйте веб-приложение](#test-the-web-app).

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>Включение SSH-подключений

SSH обеспечивает безопасный обмен данными между клиентом и контейнером. Чтобы включить SSH-подключение к контейнеру, для него необходимо настроить настраиваемый образ. Рассмотрим пример репозитория, который уже имеет необходимую конфигурацию.

* В [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile) следующий код устанавливает сервер SSH, а также задает учетные данные для входа в систему.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Эта конфигурация не разрешает внешние подключения к контейнеру. SSH доступен только на сайте Kudu и на сайте SCM. Сайты Kudu и SCM выполняют аутентификацию с вашей учетной записью Azure.

* [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) копирует файл [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config) из репозитория в каталог */etc/ssh/* .

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) предоставляет порт 2222 в контейнере. Это внутренний порт, который доступен контейнерам внутри мостовой сети виртуальной частной сети. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* [Сценарий входа](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) запускает сервер SSH.

    ```bash
    #!/bin/bash
    service ssh start
    ```

### <a name="open-ssh-connection-to-container"></a>Открытие SSH-подключения к контейнеру

SSH-подключение можно использовать только на сайте Kudu, к которому можно перейти по адресу `https://<app-name>.scm.azurewebsites.net`.

Чтобы установить подключение, перейдите по ссылке `https://<app-name>.scm.azurewebsites.net/webssh/host` и войдите, используя учетную запись Azure.

Затем вы будете перенаправлены на страницу с интерактивной консолью.

Вы можете проверить выполнение некоторых приложений в контейнере. Чтобы просмотреть контейнер и проверить выполняющиеся процессы, выполните команду `top` в командной строке.

```bash
top
```

Команда `top` выведет все запущенные процессы в контейнере.

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Поздравляем! Вы настроили настраиваемый контейнер Linux в Службе приложений.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Дополнительная информация

Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Развертывание настраиваемого образа в частном реестре контейнеров.
> * Запуск настраиваемого образа в Службе приложений.
> * Настройка переменных среды
> * Обновление и повторное развертывание образа.
> * Доступ к журналам диагностики
> * Подключение контейнера с помощью SSH.

Перейдите к следующему руководству, чтобы научиться сопоставлять пользовательские DNS-имена с приложением.

> [!div class="nextstepaction"]
> [Руководство. Сопоставление настраиваемого DNS-имени с приложением](../app-service-web-tutorial-custom-domain.md)

Также ознакомьтесь с другими ресурсами:

> [!div class="nextstepaction"]
> [Настройка настраиваемого контейнера](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Руководство. по приложению WordPress с несколькими контейнерами](tutorial-multi-container-app.md)
