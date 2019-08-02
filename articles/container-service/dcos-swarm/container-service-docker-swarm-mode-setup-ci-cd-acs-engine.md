---
title: (УСТАРЕЛО) Реализация CI/CD с использованием обработчика службы контейнеров Azure и Swarm Mode
description: Использование обработчика Службы контейнеров Azure с Docker Swarm Mode, Реестром контейнеров Azure и Azure DevOps для непрерывной доставки многоконтейнерного приложения .NET Core
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: dimart
ms.custom: mvc
ms.openlocfilehash: fe24ab21a9a7d227d58e50c58f9aff2bd91e767f
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598554"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>(УСТАРЕЛО) Реализация полного конвейера CI/CD для многоконтейнерного приложения в Службе контейнеров Azure с обработчиком ACS и Docker Swarm Mode при помощи Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Эта статья основана на [документации по ](container-service-docker-swarm-setup-ci-cd.md)полному конвейеру непрерывной интеграции и развертывания многоконтейнерного приложения в Службе контейнеров Azure с Docker Swarm с использованием Azure DevOps*.

Сегодня одной из основных сложностей при разработке современных приложений для облака является возможность предоставлять эти приложения непрерывно. Из этой статьи вы узнаете, как реализовать полный конвейер непрерывной интеграции и развертывания (далее — конвейер CI/CD) с использованием следующих компонентов: 
* обработчик Службы контейнеров Azure с Docker Swarm Mode;
* Реестр контейнеров Azure
* Azure DevOps

В этой статье используется простое приложение, доступное в [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux) и разработанное с помощью ASP.NET Core. Это приложение состоит из четырех различных служб: трех веб-API и одного веб-интерфейса.

![Пример приложения MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Цель — непрерывная доставка приложения в кластере Docker Swarm Mode с помощью Azure DevOps. На следующем рисунке детально представлен этот конвейер непрерывной доставки.

![Пример приложения MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Вот краткое описание действий:

1. Изменения кода фиксируются в репозитории исходного кода (здесь GitHub). 
2. GitHub активирует сборку в Azure DevOps. 
3. Azure DevOps получает последнюю версию источников и создает все образы, из которых состоит приложение. 
4. Azure DevOps помещает каждый образ в реестр Docker, созданный с помощью службы Реестра контейнеров Azure. 
5. Azure DevOps активирует новый выпуск. 
6. Выпуск выполняет некоторые команды с помощью SSH в главном узле кластера Службы контейнеров Azure. 
7. Docker Swarm Mode в кластере запрашивает последнюю версию образов. 
8. Новая версия приложения развертывается с помощью Docker Stack. 

## <a name="prerequisites"></a>предварительные требования

Для работы с этим руководством вам потребуется выполнить следующие задачи:

- [Создать кластер Swarm Mode в Службе контейнеров Azure с обработчиком ACS.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Подключиться к кластеру Swarm в службе контейнеров Azure.](../container-service-connect.md)
- [Создать реестр контейнеров Azure](../../container-registry/container-registry-get-started-portal.md)
- [Создать организацию и проект Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Скопировать репозиторий GitHub в свою учетную запись GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Оркестратор Docker Swarm в службе контейнеров Azure использует автономную устаревшую версию Swarm. Сейчас интегрированный [режим Swarm](https://docs.docker.com/engine/swarm/) (в Docker версии 1.12 или выше) не является поддерживаемым оркестратором в службе контейнеров Azure. По этой причине мы используем [обработчик ACS](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), предоставленный сообществом [шаблон для быстрого начала работы](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/), или решение Docker из [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Шаг 1. Настройка организации Azure DevOps 

В этом разделе вы настроите организацию Azure DevOps. Чтобы настроить конечные точки служб Azure DevOps Services, в проекте Azure DevOps на панели инструментов щелкните значок **Параметры** и выберите **Службы**.

![Откройте конечную точку служб](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Подключение Azure DevOps и учетной записи Azure

Настройте подключение между проектом Azure DevOps и учетной записью Azure.

1. Слева выберите **Создать конечную точку службы** > **Azure Resource Manager**.
2. Чтобы авторизовать Azure DevOps для работы с учетной записью Azure, выберите свою **подписку** и нажмите кнопку **ОК**.

    ![Azure DevOps — авторизация Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Подключение Azure DevOps и GitHub

Настройте подключение между проектом Azure DevOps и учетной записью GitHub.

1. Слева выберите **Создать конечную точку службы** > **GitHub**.
2. Чтобы авторизовать Azure DevOps для работы с вашей учетной записью GitHub, щелкните **Авторизовать** и следуйте указаниям в открывшемся окне.

    ![Azure DevOps — авторизация GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Подключение Azure DevOps к кластеру Службы контейнеров Azure

Последнее, что нужно сделать перед началом работы с конвейером CI/CD, — это настроить внешние подключения к кластеру Docker Swarm в Azure. 

1. Для кластера Docker Swarm добавьте конечную точку типа **SSH**. Затем введите сведения о SSH-подключении кластера Swarm (главный узел).

    ![Azure DevOps — SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Настройка завершена. На следующих шагах создается конвейер CI/CD, который создает и развертывает приложение в кластере Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Шаг 2. Создание конвейера сборки

На этом шаге настраивается конвейер сборки для проекта Azure DevOps и определяется рабочий процесс сборки для образов контейнера.

### <a name="initial-pipeline-setup"></a>Начальная настройка конвейера

1. Чтобы создать конвейер сборки, подключитесь к проекту Azure DevOps и щелкните **Build & Release** (Сборка и выпуск). В разделе **Определения сборки** щелкните **+ Создать**. 

    ![Azure DevOps — создание конвейера сборки](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Выберите **Пустой процесс**.

    ![Azure DevOps — создание пустого конвейера сборки](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Затем щелкните вкладку **Переменные** и создайте две переменные: **RegistryURL** и **AgentURL**. Вставьте значения для DNS реестра и агентов кластера.

    ![Azure DevOps — конфигурация переменных сборки](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. На странице **Определения сборки** сначала откройте вкладку **Триггеры** и настройте сборку для использования непрерывной интеграции с разветвлением проекта MyShop, созданного на этапе предварительных требований. Затем выберите **Пакетные изменения**. Убедитесь, что для параметра **Спецификация ветви** выбрано значение *docker-linux*.

    ![Azure DevOps — конфигурация репозитория сборки](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. В завершение щелкните вкладку **Параметры** и укажите для очереди агента по умолчанию значение **Hosted Linux Preview** (Размещенная предварительная версия Linux).

    ![Azure DevOps — конфигурация агента размещения](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Определение рабочего процесса сборки
Дальнейшие действия определяют рабочий процесс сборки. Сначала необходимо настроить источник кода. Для этого выберите **GitHub**, свой **репозиторий** и **ветвь** (docker-linux).

![Azure DevOps — настройка источника кода](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Для сборки приложения *MyShop* доступно пять образов контейнера. Каждый образ создается с помощью Dockerfile, расположенного в папках проекта:

* ProductsApi;
* Прокси-сервер
* RatingsApi;
* RecommendationsApi;
* ShopFront.

Для каждого образа нужны два шага Docker: один — для создания образа, а другой — для передачи образа в реестр контейнеров Azure. 

1. Чтобы добавить шаг в рабочий процесс сборки, щелкните **Добавить шаг сборки...** и выберите **Docker**.

    ![Azure DevOps — добавление шагов для сборки](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Для каждого образа настройте один шаг, в котором используется команда `docker build`.

    ![Azure DevOps — сборка Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Для операции сборки выберите свой реестр контейнеров Azure, действие **Build an image** (Создать образ) и файл Dockerfile, определяющий каждый образ. В качестве **рабочей папки** укажите корневой каталог Dockerfile, определите **имя образа** и выберите **Включить последний тег**.
    
    Имя образа должно быть представлено в формате ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Замените **[NAME]** именем образа:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Для каждого образа настройте второй шаг, на котором используется команда `docker push`.

    ![Azure DevOps — отправка Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Чтобы выполнить операцию отправки, выберите свой реестр контейнеров Azure, действие **Push an image** (Отправить образ), в соответствующем поле укажите **имя образа**, введенное на предыдущем шаге, и выберите **Включить последний тег**.

4. Настроив действия сборки и отправки для каждого из пяти образов, добавьте еще три действия в рабочий процесс сборки.

   ![Azure DevOps — добавление задачи командной строки](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Задача командной строки, которая использует bash-скрипт для замены вхождения *RegistryURL* в файле docker-compose.yml переменной RegistryURL. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure DevOps — обновление файла Compose с помощью URL-адреса реестра](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Задача командной строки, которая использует bash-скрипт для замены вхождения *AgentURL* в файле docker-compose.yml переменной AgentURL.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Задача, которая удаляет обновленный файл Compose в качестве артефакта сборки, чтобы его можно было использовать в выпуске. Подробнее показано на следующем снимке экрана.

      ![Azure DevOps — публикация артефакта](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure DevOps — публикация файла Compose](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Щелкните **Save & queue** (Сохранить и поместить в очередь), чтобы проверить конвейер сборки.

   ![Azure DevOps — "Сохранить и поместить в очередь"](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps — "Создать очередь"](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Если **сборка** выполнена правильно, должен отобразиться такой экран:

   ![Azure DevOps — "Сборка успешно выполнена"](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Шаг 3. Создание конвейера выпуска

Azure DevOps позволяет [управлять выпусками в разных средах](https://www.visualstudio.com/team-services/release-management/). Вы можете включить непрерывное развертывание, чтобы ваше приложение беспрепятственно развертывалось в разных средах (например, в среде разработки, тестирования, подготовки и рабочей среде). Вы также можете создать среду, которая представляет кластер Docker Swarm Mode Службы контейнеров Azure.

![Azure DevOps — выпуск в ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Начальная настройка выпуска

1. Чтобы создать конвейер выпуска, щелкните **Выпуски** >  **+ Release** (+ Выпуск).

2. Чтобы настроить источник артефакта, выберите **Артефакты** > **Link an artifact source** (Связать источник артефакта). Свяжите этот новый конвейер выпуска со сборкой, указанной на предыдущем шаге. После этого файл docker-compose.yml появится в процессе выпуска.

    ![Azure DevOps — выпуск артефактов](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Чтобы настроить триггер выпуска, выберите **Триггеры** и **Непрерывное развертывание**. Установите триггер в том же источнике артефакта. Этот параметр обеспечивает запуск нового выпуска после успешного завершения сборки.

    ![Azure DevOps — выпуск триггеров](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Чтобы настроить переменные выпуска, щелкните **Переменные** и выберите **+Переменная**, чтобы создать три новые переменные с данными из реестра: **docker.username**, **docker.password** и **docker.registry**. Вставьте значения для DNS реестра и агентов кластера.

    ![Azure DevOps — конфигурация репозитория сборки](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Как показано на предыдущем снимке экрана, щелкните флажок **блокировки** в docker.password. Этот параметр важен для ограничения пароля.
    >

### <a name="define-the-release-workflow"></a>Определение рабочего процесса выпуска

Рабочий процесс выпуска состоит из двух задач, которые необходимо добавить.

1. Настройте задачу для безопасного копирования файла Compose в папку *deploy* на главном узле Docker Swarm через подключение SSH, настроенное ранее. Подробнее показано на следующем снимке экрана.
    
    Исходная папка: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Azure DevOps — выпуск SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Настройте вторую задачу для выполнения команды bash, чтобы запустить команды `docker` и `docker stack deploy` на главном узле. Подробнее показано на следующем снимке экрана.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps — выпуск Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    На главном узле выполняется CLI-команда Docker и Docker-Compose, выполняющая следующие задачи:

   - вход в реестр контейнеров Azure (в нем используются три переменные сборки, указанные на вкладке **Переменные**);
   - определение переменной **DOCKER_HOST** для работы с конечной точкой Swarm (:2375);
   - переход к папке *deploy*, созданной при выполнении предыдущей задачи безопасного копирования, содержащей файл docker-compose.yml; 
   - выполнение команд `docker stack deploy`, которые извлекают новые образы и создают контейнеры.

     >[!IMPORTANT]
     > Как показано на предыдущем снимке экрана, не устанавливайте флажок **Ошибка в STDERR**. Этот параметр позволяет завершить процесс выпуска, так как `docker-compose` выводит несколько диагностических сообщений, например об остановке контейнеров или их удалении, в стандартном выводе данных об ошибках. Если флажок установлен, Azure DevOps сообщает об ошибках, произошедших во время выпуска, даже если все работает правильно.
     >
3. Сохраните этот новый конвейер выпуска.

## <a name="step-4-test-the-cicd-pipeline"></a>Шаг 4. Тестирование конвейера CI/CD

Теперь после выполнения настройки пришло время протестировать этот новый конвейер CI/CD. Самый простой способ сделать это — обновить исходный код и сохранить изменения в репозитории GitHub. Через несколько секунд после отправки кода в Azure DevOps появится новая сборка. После успешного выполнения новый выпуск активируется и разворачивает новую версию приложения в кластере Службы контейнеров Azure.

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о CI/CD с Azure DevOps см. в статье [Azure pipelines документация](/azure/devops/pipelines/?view=azure-devops) .
* Дополнительные сведения об обработчике ACS см. в [репозитории GitHub для обработчика ACS](https://github.com/Azure/acs-engine).
* Дополнительные сведения о Docker Swarm Mode см. в [этой статье](https://docs.docker.com/engine/swarm/).
