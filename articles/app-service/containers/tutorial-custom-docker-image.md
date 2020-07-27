---
title: Руководство по Сборка и выполнение пользовательского образа в Службе приложений Azure
description: В этом пошаговом руководстве описано, как создать пользовательский образ Linux, отправить его в Реестр контейнеров Azure, а затем развернуть этот образ в Службе приложений Azure.
keywords: служба приложений azure, веб-приложение, docker, контейнер
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
ms.custom: mvc, seodec18, tracking-python
ms.openlocfilehash: bfe1e9fd2532e308c474aee6983615e28a8081f5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506857"
---
# <a name="tutorial-run-a-custom-docker-image-in-app-service"></a>Руководство по Выполнение пользовательского образа Docker в Службе приложений.

Служба приложений Azure использует технологию контейнеров Docker для размещения встроенных и пользовательских образов. Чтобы просмотреть список встроенных образов, выполните команду Azure CLI [az webapp list-runtimes --linux](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes). Если эти образы не соответствуют вашим требованиям, вы можете создать и развернуть пользовательский образ.

В этом руководстве описано следующее:

> [!div class="checklist"]
> * создание пользовательского образа, если ни один из встроенных не соответствует вашим требованиям;
> * отправка пользовательского образа в частный реестр контейнеров в Azure;
> * Запуск настраиваемого образа в Службе приложений.
> * Настройка переменных среды
> * Обновление и повторное развертывание образа.
> * Доступ к журналам диагностики
> * Подключение контейнера с помощью SSH.

Выполнение инструкций из этого руководства связано с небольшими расходами для учетной записи Azure за создание реестра контейнеров, и, возможно, за размещение контейнера на срок более месяца.

## <a name="set-up-your-initial-environment"></a>Настройка начальной среды

* Подготовьте учетную запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно.
* Установите [Docker](https://docs.docker.com/get-started/#setup) для создания образов Docker. Для установки Docker может потребоваться перезагрузка компьютера.
* Установите <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> версии 2.0.80 или более поздней для выполнения команд в любой оболочке для подготовки и настройки ресурсов Azure.

После установки Docker и Azure CLI откройте окно терминала и убедитесь, что вы все готово к использованию Docker.

```bash
docker --version
```

Также убедитесь, что у вас есть Azure CLI версии 2.0.80 или выше.

```azurecli
az --version
```

Теперь войдите в Azure с помощью CLI.

```azurecli
az login
```

Команда `az login` открывает окно браузера для ввода учетных данных. После выполнения команда отображает выходные данные JSON с информацией о подписках.

Войдя, вы сможете выполнять в Azure CLI команды Azure для работы с ресурсами в подписке.

## <a name="clone-or-download-the-sample-app"></a>Клонирование или скачивание примера приложения

Чтобы получить пример для работы с этим руководством, его можно клонировать с помощью Git или скачать.

### <a name="clone-with-git"></a>Клонирование с помощью Git

Клонируйте репозиторий примера.

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Не забудьте включить аргумент `--config core.autocrlf=input`, чтобы обеспечить правильное завершение строк в файлах, которые используются в контейнере Linux.

Затем перейдите в эту папку:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Загрузка с GitHub

Вместо клонирования с помощью Git вы можете открыть страницу [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux), а затем выбрать действие **Clone** (Клонировать) и **Download ZIP** (Скачать ZIP-файл). 

Распакуйте ZIP-файл в папку с именем *docker-django-webapp-linux*. 

Затем откройте окно терминала в этой папке *docker-django-webapp-linux*.

## <a name="optional-examine-the-docker-file"></a>(Необязательно) Анализ файл Docker

В нашем примере файл с именем _Dockerfile_ описывает образ Docker и содержит инструкции по настройке.

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
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

* Первая группа команд устанавливает в среде обязательные для приложения компоненты.
* Вторая группа команд создает сервер [SSH](https://www.ssh.com/ssh/protocol/) для безопасного взаимодействия между контейнером и узлом.
* В последней строке файла (`ENTRYPOINT ["init.sh"]`) вызывается `init.sh` для запуска службы SSH и сервера Python.

## <a name="build-and-test-the-image-locally"></a>Сборка и тестирование образа в локальной среде

1. Запустите сборку образа с помощью следующей команды.

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Убедитесь, что сборка работает, запустив контейнер Docker на локальном компьютере.

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    Эта команда [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) задает порт с помощью аргумента `-p`, за которым следует имя образа. 
    
    > [!TIP]
    > Если вы работаете в среде Windows и видите ошибку вида *standard_init_linux.go:211: exec user process caused "no such file or directory"* , значит в файле *init.sh* для завершения строк используется CR-LF, а не ожидаемый вариант LF. Эта ошибка возникает, если вы клонировали пример репозитория с помощью Git, но не включили параметр `--config core.autocrlf=input`. В этом случае повторите клонирование репозитория с правильным аргументом --config. Вы также можете получить аналогичную ошибку, если редактировали файл*init.sh* и сохранили его с использованием CR-LF для завершения. В этом случае сохраните его снова, используя LF.

1. Перейдите по адресу `http://localhost:8000` и убедитесь, что веб-приложение и контейнер функционируют правильно.

    ![Локальное тестирование веб-приложения](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Создание группы ресурсов

В этом и последующих разделах показано, как подготовить в Azure ресурсы, затем отправить в них образ и развернуть контейнер в Службе приложений Azure. Для начала создайте группу ресурсов, в которой будут собраны все эти ресурсы.

Выполните команду [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create), чтобы создать группу ресурсов.

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

Вы можете изменить значение `--location`, чтобы указать близкий к вам регион.

## <a name="push-the-image-to-azure-container-registry"></a>Принудительная отправка образов в Реестр контейнеров Azure

В этом разделе вы отправите образ в Реестр контейнеров Azure, откуда Служба приложений сможет его развернуть.

1. Выполните команду [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create), чтобы создать Реестр контейнеров Azure.

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    Замените `<registry-name>` понятным именем реестра. Это имя может содержать только буквы и цифры и должно быть уникальным в пределах Azure.

1. Выполните команду [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show), чтобы получить учетные данные для этого реестра.

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    Выходные данные этой команды содержат два пароля и имя пользователя для реестра в формате JSON.
    
1. Выполните команду `docker login` для входа в реестр контейнеров:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    Замените `<registry-name>` и `<registry-username>` значениями из предыдущих шагов. При появлении запроса введите один из паролей из предыдущего шага.

    Используйте одно и то же имя реестра во всех остальных шагах этого раздела.

1. Войдя, присвойте локальному образу Docker тег для реестра.

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. С помощью команды `docker push` отправьте образ в реестр.

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Первая отправка образа может занять несколько минут, так как она включает базовый образ. Все последующие отправки обычно выполняются быстрее.

    Пока вы ждете завершения, вы можете выполнить шаги, описанные в следующем разделе, то есть настроить развертывание из реестра в Службе приложений.

1. Чтобы проверить успешность принудительной отправки, выполните команду `az acr repository list`.

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    В выходных данных должно отобразиться имя отправленного образа.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>Настройка развертывания образа из реестра в Службе приложений

Чтобы развернуть контейнер в Службе приложений Azure, сначала создайте веб-приложение в Службе приложений, а затем подключите это веб-приложение к реестру контейнеров. При запуске веб-приложения Служба приложений автоматически извлечет этот образ из реестра.

1. Создайте план Службы приложений с помощью команды [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    План Службы приложений соответствует виртуальной машине, в которой размещено веб-приложение. По умолчанию предыдущая команда использует бюджетную [ценовую категорию B1](https://azure.microsoft.com/pricing/details/app-service/linux/), которая предоставляется в течение первого месяца бесплатно. Уровень можно изменить с помощью параметра `--sku`.

1. Создайте веб-приложение с помощью команды [`az webpp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create).

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Замените здесь `<app-name>` именем веб-приложения, которое должно быть уникальным в пределах Azure. Также замените `<registry-name>` именем реестра из предыдущего раздела.

1. Используйте [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set), чтобы задать переменную среды `WEBSITES_PORT`, которую ожидает код приложения. 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Замените `<app-name>` значением, которое вы использовали на предыдущем шаге.
    
    Дополнительные сведения об этой переменной среды см. в [файле сведений в репозитории этого примера](https://github.com/Azure-Samples/docker-django-webapp-linux) на сайте GitHub.

1. Включите [управляемое удостоверение](/azure/app-service/overview-managed-identity) для веб-приложения, выполнив команду [`az webapp identity assign`](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign).

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    Замените `<app-name>` значением, которое вы использовали на предыдущем шаге. Отфильтровав выходные данные этой команды с помощью аргументов `--query` и `--output`, вы найдете субъект-службу назначенного удостоверения, который вам вскоре потребуется.

    Управляемое удостоверение позволяет предоставить веб-приложению разрешения на доступ к другим ресурсам Azure, не создавая специальные учетные данные.

1. Получите с помощью команды [`az account show`](/cli/azure/account?view=azure-cli-latest#az-account-show) идентификатор подписки, который вам понадобится на следующем шаге.

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Предоставьте веб-приложению разрешение на доступ к реестру контейнеров.

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Измените следующие значения:
    - `<principal-id>` на идентификатор субъекта-службы, полученный командой `az webapp identity assign`;
    - `<registry-name>` на имя реестра контейнеров;
    - `<subscription-id>` на идентификатор подписки, полученный командой `az account show`.

Дополнительные сведения об этих разрешениях см. в статье [Общие сведения об управлении доступом на основе ролей (RBAC) для ресурсов Azure](/azure/role-based-access-control/overview). 

## <a name="deploy-the-image-and-test-the-app"></a>Развертывание образа и тестирование приложения

Вы сможете выполнить эти шаги, когда образ будет передан в реестр контейнеров, а Служба приложений полностью подготовлена.

1. С помощью команды [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) укажите реестр контейнеров и образ, который нужно развернуть для веб-приложения.

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    Замените `<app_name>` именем веб-приложения, а два вхождения `<registry-name>` — именем реестра. 

    - При использовании реестра, отличающегося от Docker Hub (как в представленном примере), значение `--docker-registry-server-url` должно быть отформатировано, чтобы содержать `https://` и полное доменное имя реестра.
    - Сообщение No credential was provided to access Azure Container Registry. Trying to look up... (Учетные данные для доступа к Реестру контейнеров Azure не предоставлены. Выполняется поиск...) означает, что Azure использует для проверки подлинности в реестре контейнеров управляемое удостоверение, а не запрашивает имя и пароль.
    - Если вы встретите ошибку AttributeError: 'NoneType' object has no attribute 'reserved' (AttributeError: объект NoneType не имеет атрибута reserved), проверьте правильность `<app-name>`.

    > [!TIP]
    > Вы можете в любой момент получить параметры контейнера для веб-приложения с помощью команды `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg`. Этот образ задается в свойстве `DOCKER_CUSTOM_IMAGE_NAME`. Когда веб-приложение развертывается через шаблоны Azure DevOps или Azure Resource Manager, этот же образ может отображаться и в свойстве с именем `LinuxFxVersion`. Оба этих свойства имеют одинаковое назначение. Если они оба присутствуют в конфигурации веб-приложения, приоритет отдается `LinuxFxVersion`.

1. Когда команда `az webapp config container set` будет выполнена, веб-приложение должно работать в контейнере в Службе приложений.

    Чтобы проверить приложение, перейдите по адресу `http://<app-name>.azurewebsites.net`, заменив `<app-name>` именем веб-приложения. При первом входе ответ от приложения может поступить с некоторой задержкой, так как Служба приложений должна получить из реестра полный образ. Если время ожидания браузера истечет, просто обновите страницу. После получения начального образа все последующие тесты выполняются намного быстрее.

    ![Успешный тест веб-приложения в Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>Изменение кода и повторное развертывание приложения

В этом разделе вы внесете изменения в код веб-приложения, перестроите контейнер и отправите его в реестр. Затем Служба приложений автоматически извлечет обновленный образ из реестра и обновит работающее веб-приложение.

1. В локальной папке *docker-django-webapp-linux* откройте файл *app/templates/app/index.html*.

1. Измените первый элемент HTML в соответствии со следующим кодом.

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. Сохраните изменения.

1. Перейдите в папку *docker-django-webapp-linux* и перестройте образ.

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. В теге этого образа укажите новый тег номера версии (v1.0.1).

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Замените `<registry-name>` именем реестра.

1. Отправьте образ в реестр:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Перезапустите веб-приложение:

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    Замените `<app_name>` именем веб-приложения. После перезагрузки Служба приложений извлечет обновленный образ из реестра контейнеров.

1. Проверьте успешность развертывания обновления, перейдя по адресу `http://<app-name>.azurewebsites.net`.

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

1. Включите ведение журнала для контейнера.

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. Включите потоковую передачу журналов.

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    Если журналы консоли не отображаются, проверьте еще раз через 30 секунд.

    Вы также можете проверить файлы журнала в браузере на странице `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

1. Чтобы остановить потоковую передачу журналов, нажмите клавиши **CTRL**+**C**.

## <a name="connect-to-the-container-using-ssh"></a>Подключение контейнера с помощью SSH.

SSH обеспечивает безопасный обмен данными между клиентом и контейнером. Чтобы включить SSH-подключение к контейнеру, для него необходимо настроить настраиваемый образ. После запуска контейнера можно открыть SSH-подключение к нему.

### <a name="configure-the-container-for-ssh"></a>Настройка SSH для контейнера

Пример приложения в этом руководстве уже имеет *Dockerfile* с необходимой конфигурацией, которая устанавливает SSH-сервер и задает учетные данные для входа. Этот раздел не содержит практических действий. Чтобы подключиться к контейнеру, перейдите к следующему разделу.

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> Эта конфигурация не допускает внешние подключения к контейнеру. SSH доступен только на сайте Kudu и на сайте SCM. Сайты Kudu и SCM выполняют аутентификацию с вашей учетной записью Azure.

Также этот *Dockerfile* копирует файл *sshd_config* в папку */etc/ssh/* и предоставляет порт 2222 в контейнере.

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

Внутренний порт 2222 доступен контейнерам внутри мостовой сети виртуальной частной сети. 

Наконец, начальный сценарий *init.sh* запускает SSH-сервер.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>Открытие SSH-подключения к контейнеру

1. Чтобы установить подключение, перейдите по адресу `https://<app-name>.scm.azurewebsites.net/webssh/host` и войдите с учетной записью Azure. Замените `<app-name>` именем веб-приложения.

1. Войдя, вы попадете на информационную страницу веб-приложения. Выберите **SSH** в верхней части страницы, чтобы открыть оболочку и выполнить команды.

    Например, здесь можно изучить выполняемые процессы с помощью команды `top`.
    
## <a name="clean-up-resources"></a>Очистка ресурсов

За использование ресурсов, созданных с помощью этой статьи, может взиматься плата. Чтобы очистить ресурсы, достаточно удалить только группу ресурсов, которая их содержит.

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

## <a name="next-steps"></a>Дальнейшие действия

Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Развертывание настраиваемого образа в частном реестре контейнеров.
> * Развертывание и запуск пользовательского образа в Службе приложений
> * Обновление и повторное развертывание образа.
> * Доступ к журналам диагностики
> * Подключение контейнера с помощью SSH.

Перейдите к следующему руководству, в котором описано, как сопоставлять пользовательские DNS-имена с приложением.

> [!div class="nextstepaction"]
> [Руководство. Сопоставление настраиваемого DNS-имени с приложением](../app-service-web-tutorial-custom-domain.md)

Также ознакомьтесь с другими ресурсами:

> [!div class="nextstepaction"]
> [Настройка пользовательского контейнера](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Руководство. по приложению WordPress с несколькими контейнерами](tutorial-multi-container-app.md)
