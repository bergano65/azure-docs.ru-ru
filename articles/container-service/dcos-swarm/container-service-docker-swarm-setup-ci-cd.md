---
title: Непрерывная интеграция и непрерывное развертывание со использованием службы контейнеров Azure и Swarm
description: Использование Службы контейнеров Azure с Docker Swarm, Реестром контейнеров Azure и Azure DevOps для непрерывной доставки многоконтейнерного приложения .NET Core
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 3b91c269104e740add1d3a5b8ecaee93ca269188
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302832"
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>Полный конвейер средств непрерывной интеграции и доставки для развертывания многоконтейнерного приложения в Службе контейнеров Azure с Docker Swarm с использованием Azure DevOps Services

Одна из основных сложностей при разработке современных приложений для облака — возможность предоставлять эти приложения непрерывно. Из этой статьи вы узнаете, как реализовать полный конвейер непрерывной интеграции и развертывания (CI/CD) с помощью Службы контейнеров Azure с Docker Swarm, Реестра контейнеров Azure, а также управления Azure Pipelines.

В этой статье используется простое приложение, доступное в [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs) и разработанное с помощью ASP.NET Core. Это приложение состоит из четырех различных служб: трех веб-API и одного веб-интерфейса.

![Пример приложения MyShop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Цель — непрерывная доставка приложения в кластере Docker Swarm с помощью Azure DevOps Services. На следующем рисунке детально представлен этот конвейер непрерывной доставки.

![Пример приложения MyShop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Вот краткое описание действий:

1. Изменения кода фиксируются в репозитории исходного кода (здесь GitHub). 
1. GitHub активирует сборку в Azure DevOps Services. 
1. Azure DevOps Services получает последнюю версию источников и создает все образы, из которых состоит приложение. 
1. Azure DevOps Services помещает каждый образ в реестр Docker, созданный с помощью службы Реестра контейнеров Azure. 
1. Azure DevOps Services активирует новый выпуск. 
1. Выпуск выполняет некоторые команды с помощью SSH в главном узле кластера Службы контейнеров Azure. 
1. Docker Swarm в кластере извлекает последнюю версию образов. 
1. Развертывается новая версия приложения с помощью Docker Compose. 

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется выполнить следующие задачи:

- [Создайте кластер Swarm в службе контейнеров Azure.](container-service-deployment.md)
- [Подключитесь к кластеру Swarm в службе контейнеров Azure.](../container-service-connect.md)
- [Создать реестр контейнеров Azure](../../container-registry/container-registry-get-started-portal.md)
- [Создать организацию и проект Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student).
- [Скопировать репозиторий GitHub в свою учетную запись GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Вам также понадобится компьютер Ubuntu (14.04 или 16.04) с установленным программным обеспечением Docker. Этот компьютер используется службами Azure DevOps Services во время процессов Azure Pipelines. Этот компьютер можно создать, используя образ, доступный в [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Шаг 1. Настройка организации Azure DevOps Services 

В этом разделе вы настроите организацию Azure DevOps Services.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Настройка агента сборки Azure DevOps Services Linux

Для создания образов Docker и их отправки в реестр контейнеров Azure из сборки Azure DevOps Services необходимо зарегистрировать агент Linux. Доступны следующие варианты установки:

* [Развертывание агента на платформе Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Использование Docker для запуска агента Azure DevOps Services](https://hub.docker.com/r/microsoft/vsts-agent).

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Установка расширения Azure DevOps Services Docker Integration

Корпорация Майкрософт предоставляет расширение Azure DevOps Services для работы с Docker в процессах Azure Pipelines. Это расширение доступно в [Azure DevOps Services Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Нажмите кнопку **Установить** для добавления этого расширения в организацию Azure DevOps Services:

![Установка Docker Integration](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Вам будет предложено подключиться к организации Azure DevOps Services, используя свои учетные данные. 

### <a name="connect-azure-devops-services-and-github"></a>Подключение Azure DevOps Services и GitHub

Настройте подключение между проектом Azure DevOps Services и учетной записью GitHub.

1. В проекте Azure DevOps Services на панели инструментов щелкните значок **Параметры** и выберите **Службы**.

    ![Azure DevOps Services — внешнее подключение](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. Слева выберите **Создать конечную точку службы** > **GitHub**.

    ![Azure DevOps Services — GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Чтобы авторизовать Azure DevOps Services для работы с вашей учетной записью GitHub, щелкните **Авторизовать** и следуйте указаниям в открывшемся окне.

    ![Azure DevOps Services — авторизация GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Подключение Azure DevOps Services к Реестру контейнеров Azure и кластеру Службы контейнеров Azure

Последнее, что нужно сделать перед началом работы с конвейером CI/CD — это настроить внешние подключения к реестру контейнеров и кластеру Docker Swarm в Azure. 

1. В параметрах **служб** проекта Azure DevOps Services добавьте конечную точку службы типа **Docker Registry**. 

1. В открывшемся всплывающем окне введите URL-адрес и учетные данные реестра контейнеров Azure.

    ![Azure DevOps Services — реестр Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Для кластера Docker Swarm добавьте конечную точку типа **SSH**. Затем введите сведения о подключении SSH кластера Swarm.

    ![Azure DevOps Services — SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Настройка завершена. На следующих шагах создается конвейер CI/CD, который создает и развертывает приложение в кластере Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Шаг 2. Создание конвейера сборки

На этом шаге настраивается конвейер сборки для проекта Azure DevOps Services и определяется рабочий процесс сборки для образов контейнера.

### <a name="initial-pipeline-setup"></a>Начальная настройка конвейера

1. Чтобы создать конвейер сборки, подключитесь к проекту Azure DevOps Services и щелкните **Build & Release** (Сборка и выпуск). 

1. В разделе **Определения сборки** щелкните **+ Создать**. Выберите шаблон **Пустой**.

    ![Azure DevOps — создание конвейера сборки](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Настройте новую сборку с помощью источника репозитория GitHub, установите флажок **Непрерывная интеграция** и выберите очередь агента, в которой зарегистрирован агент Linux. Нажмите кнопку **Создать**, чтобы создать конвейер сборки.

    ![Azure DevOps Services — создание конвейера сборки](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. На странице **Определения сборки** сначала откройте вкладку **Репозиторий** и настройте сборку для использования разветвления проекта MyShop, созданного на этапе предварительных требований. Выберите *acs-docs* в качестве значения для поля **Ветвь по умолчанию**.

    ![Azure DevOps Services — конфигурация репозитория сборки](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. На вкладке **Триггеры** настройте активацию сборки после выполнения каждой фиксации. Выберите **Непрерывная интеграция** и **Пакетные изменения**.

    ![Azure DevOps Services — конфигурация триггера сборки](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Определение рабочего процесса сборки
Дальнейшие действия определяют рабочий процесс сборки. Для сборки приложения *MyShop* доступно пять образов контейнера. Каждый образ создается с помощью Dockerfile, расположенного в папках проекта:

* ProductsApi;
* Прокси-сервер
* RatingsApi;
* RecommandationsApi;
* ShopFront.

Для каждого образа необходимо добавить два шага Docker: один для создания образа, а другой для передачи образа в реестр контейнеров Azure. 

1. Чтобы добавить шаг в рабочий процесс сборки, щелкните **Добавить шаг сборки...** и выберите **Docker**.

    ![Azure DevOps Services — добавление шагов для сборки](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Для каждого образа настройте один шаг, в котором используется команда `docker build`.

    ![Azure DevOps Services — сборка Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Для операции сборки выберите реестр контейнеров Azure, действие **Build an image** (Создать образ) и Dockerfile, определяющий каждый образ. В поле **Build context** (Контекст сборки) введите имя корневого каталога Dockerfile и укажите **имя образа** в соответствующем поле. 
    
    Как показано на предыдущем снимке экрана, имя образа должно начинаться с универсального кода ресурса (URI) в реестре контейнеров Azure. (Чтобы параметризовать тег образа, также можно использовать переменную сборки, например идентификатор сборки в этом примере.)

1. Для каждого образа настройте второй шаг, на котором используется команда `docker push`.

    ![Azure DevOps Services — отправка Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Чтобы выполнить операцию принудительной отправки, выберите реестр контейнеров Azure, действие **Push an image** (Отправить образ) и укажите **имя образа**, введенное на предыдущем шаге, в соответствующем поле.

1. Настроив действия сборки и принудительной отправки для каждого из пяти образов, добавьте еще два действия в рабочий процесс сборки.

    a. Задача командной строки, которая использует bash-скрипт для замены вхождения *BuildNumber* в файле docker-compose.yml текущим идентификатором сборки. Подробнее показано на следующем снимке экрана.

    ![Azure DevOps Services — обновление файла Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Задача, которая удаляет обновленный файл Compose в качестве артефакта сборки, чтобы его можно было использовать в выпуске. Подробнее показано на следующем снимке экрана.

    ![Azure DevOps Services — публикация файла Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Щелкните **Сохранить** и присвойте имя конвейеру сборки.

## <a name="step-3-create-the-release-pipeline"></a>Шаг 3. Создание конвейера выпуска

Azure DevOps Services позволяет [управлять выпусками в разных средах](https://www.visualstudio.com/team-services/release-management/). Вы можете включить непрерывное развертывание, чтобы ваше приложение беспрепятственно развертывалось в разных средах (например, в среде разработки, тестирования, подготовки и рабочей среде). Вы также можете создать среду, которая представляет кластер Docker Swarm Службы контейнеров Azure.

![Azure DevOps Services — выпуск в ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Начальная настройка выпуска

1. Чтобы создать конвейер выпуска, щелкните **Выпуски** > **+ Release** (+ Выпуск).

1. Чтобы настроить источник артефакта, выберите **Артефакты** > **Link an artifact source** (Связать источник артефакта). Свяжите этот новый конвейер выпуска со сборкой, указанной на предыдущем шаге. После этого файл docker-compose.yml появится в процессе выпуска.

    ![Azure DevOps Services — артефакты выпуска](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Чтобы настроить триггер выпуска, выберите **Триггеры** и **Непрерывное развертывание**. Установите триггер в том же источнике артефакта. Этот параметр обеспечивает запуск нового выпуска сразу после успешного завершения сборки.

    ![Azure DevOps Services — триггеры выпуска](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Определение рабочего процесса выпуска

Рабочий процесс выпуска состоит из двух задач, которые необходимо добавить.

1. Настройте задачу для безопасного копирования файла Compose в папку *deploy* на главном узле Docker Swarm через подключение SSH, настроенное ранее. Подробнее показано на следующем снимке экрана.

    ![Azure DevOps Services — выпуск SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Настройте вторую задачу для выполнения команды bash, чтобы запустить команды `docker` и `docker-compose` на главном узле. Подробнее показано на следующем снимке экрана.

    ![Azure DevOps Services — выпуск Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    В команде, выполняемой на главном узле, используется интерфейс командной строки Docker и Docker-Compose для выполнения следующих задач:

    - вход в реестр контейнеров Azure (в нем используются три переменные сборки, указанные на вкладке **Переменные**);
    - определение переменной **DOCKER_HOST** для работы с конечной точкой Swarm (:2375);
    - переход к папке *deploy*, созданной при выполнении предыдущей задачи безопасного копирования, содержащей файл docker-compose.yml; 
    - выполнение команд `docker-compose`, которые извлекают новые образы, останавливают работу служб, удаляют службы и создают контейнеры.

    >[!IMPORTANT]
    > Как показано на предыдущем снимке экрана, не устанавливайте флажок **Ошибка в STDERR**. Это важный параметр, так как `docker-compose` выводит несколько диагностических сообщений, например об остановке контейнеров или их удалении, в стандартном выводе данных об ошибках. Если флажок установлен, Azure DevOps Services сообщает об ошибках, произошедших во время выпуска, даже если все работает правильно.
    >
1. Сохраните этот новый конвейер выпуска.


>[!NOTE]
>В этом развертывании допускается некоторое время простоя, так как мы останавливаем старые службы и запускаем новые. Этого можно избежать, выполнив "сине-зеленое" развертывание.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Шаг 4. Тестирование конвейера CI/CD

Теперь после выполнения настройки пришло время протестировать этот новый конвейер CI/CD. Самый простой способ сделать это — обновить исходный код и сохранить изменения в репозитории GitHub. Через несколько секунд после отправки кода в Azure DevOps Services появится новая сборка. После успешного выполнения новый выпуск будет активирован и развернет новую версию приложения в кластере Службы контейнеров Azure.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о CI/CD в Azure DevOps Services см. на странице об [Azure Pipelines](https://www.visualstudio.com/docs/build/overview).
