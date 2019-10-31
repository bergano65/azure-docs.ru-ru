---
title: Устранение неисправностей
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Быстрая разработка Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: 'Docker, Kubernetes, Azure, служба контейнеров Azure, служба Azure Kubernetes, контейнеры, Helm, сетка службы, сетка службы маршрутизации, kubectl, k8s '
ms.openlocfilehash: e145c234c7fc0bc7b9263f40f22d3fd90c1b7250
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064115"
---
# <a name="troubleshooting-guide"></a>Руководство по устранению неполадок

Это руководство содержит сведения о распространенных проблемах, которые могут возникнуть при использовании Azure Dev Spaces.

Если при использовании Azure Dev Spaces возникла проблема, создайте [проблему в репозитории Azure dev Spaces GitHub](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Перед началом работы

Чтобы более эффективно устранять неполадки, можно создать более подробные журналы для проверки.

Для расширения Visual Studio задайте переменной среды `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` значение 1. Обязательно перезапустите Visual Studio, чтобы изменения переменной среды вступили в силу. После включения подробные журналы будут записаны в каталог `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

В CLI вы можете выводить больше сведений во время выполнения команды, используя параметр `--verbose`. Вы также можете просмотреть более подробные журналы в `%TEMP%\Azure Dev Spaces`. На компьютере Mac каталог TEMP можно найти, выполнив `echo $TMPDIR` в окне терминала. На компьютере с ОС Linux каталог TEMP обычно называется `/tmp`.

Azure Dev Spaces также лучше работает при отладке одного экземпляра или Pod. Файл `azds.yaml` содержит параметр *replicaCount*, который указывает количество модулей Pod, которое Kubernetes выполняется для вашей службы. Если вы измените *replicaCount* , чтобы настроить приложение для запуска нескольких модулей Pod для определенной службы, отладчик подключится к первому модулю, если в списке указано в алфавитном порядке. Отладчик присоединяется к другому модулю pod при перезапуске оригинального модуля pod, что может привести к непредвиденному поведению.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Распространенные проблемы при включении Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Ошибка "не удалось создать контроллер Azure Dev Spaces"

Вы можете увидеть эту ошибку, если что-то пойдет не так с созданием контроллера. Если это временная ошибка, ее можно устранить, удалив контроллер, а затем повторно создав его.

Вы также можете попытаться удалить контроллер:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Для удаления контроллера используйте Azure Dev Spaces CLI. Невозможно удалить контроллер из Visual Studio. Вы также не можете установить Azure Dev Spaces CLI в Azure Cloud Shell, чтобы удалить контроллер из Azure Cloud Shell.

Если у вас нет установленного интерфейса командной строки Azure Dev Spaces, его можно сначала установить с помощью следующей команды, а затем удалить контроллер:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Чтобы повторно создать контроллер, можно использовать CLI или Visual Studio. Примеры см. в руководствах по [коллективной разработке](quickstart-team-development.md) или [разработке с помощью .NET Core](quickstart-netcore-visualstudio.md) .

### <a name="controller-create-failing-because-of-controller-name-length"></a>Сбой создания контроллера из-за длины имени контроллера

Длина имени контроллера Azure Dev Spaces не может превышать 31 символ. Если имя контроллера превышает 31 символ при включении пространств разработки в кластере AKS или создании контроллера, появится сообщение об ошибке. Пример.

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Чтобы устранить эту проблему, создайте контроллер с альтернативным именем. Пример.

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Сбой включения пространств разработки при добавлении пулов узлов Windows в кластер AKS

В настоящее время Azure Dev Spaces предназначена только для модулей Pod и узлов Linux. При наличии кластера AKS с пулом узлов Windows необходимо убедиться, что Azure Dev Spaces модули Pod запланированы только на узлах Linux. Если на узле Windows запланировано выполнение Azure Dev Spaces Pod, то этот модуль не запускается и не включает пространства разработки.

Чтобы устранить эту проблему, [добавьте таинт](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) в кластер AKS, чтобы не выполнять запланированное выполнение модулей Linux на узле Windows.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Ошибка "не найдено узлов Linux унтаинтед в состоянии готовности в кластере. Для развертывания модулей Pod в пространстве имен "аздс" должен быть хотя бы один узел унтаинтед Linux в состоянии "Готово". "

Azure Dev Spaces не удалось создать контроллер в кластере AKS, так как ему не удалось найти узел унтаинтед в состоянии *готовности* для планирования модулей Pod. Azure Dev Spaces требуется по крайней мере один узел Linux в состоянии *готовности* , которое позволяет выполнять планирование для модулей Pod без указания допусков.

Чтобы устранить эту проблему, [Обновите конфигурацию таинт](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) в КЛАСТЕРе AKS, чтобы убедиться, что по крайней мере один узел Linux позволяет выполнять планирование для модулей Pod без указания допусков. Кроме того, убедитесь, что по крайней мере один узел Linux, позволяющий выполнять планирование для модулей Pod без указания допусков, находится в состоянии *Готово* . Если для достижения состояния *готовности* узла требуется много времени, можно попробовать перезапустить узел.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Ошибка "Azure Dev Spaces неправильной установки CLI" при запуске `az aks use-dev-spaces`

Обновление Azure Dev Spaces CLI изменило путь установки. Если вы используете версию Azure CLI более раннюю, чем 2.0.63, может появиться эта ошибка. Чтобы отобразить версию Azure CLI, используйте `az --version`.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Несмотря на сообщение об ошибке при запуске `az aks use-dev-spaces` с версией Azure CLI перед 2.0.63, установка выполнена не будет. Вы можете продолжать использовать `azds` без каких бы то ни было проблем.

Чтобы устранить эту проблему, обновите установку [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) до 2.0.63 или более поздней версии. Это обновление позволяет устранить сообщение об ошибке, полученное при запуске `az aks use-dev-spaces`. Кроме того, можно продолжить использовать текущую версию Azure CLI и CLI Azure Dev Spaces.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Распространенные проблемы при подготовке проекта к Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Предупреждение "не удалось создать Dockerfile из-за неподдерживаемого языка"
Azure Dev Spaces предоставляет встроенную поддержку C# и Node.js. При запуске `azds prep` в каталоге с кодом, написанным на одном из этих языков, Azure Dev Spaces автоматически создает соответствующий Dockerfile.

Вы по-прежнему можете использовать Azure Dev Spaces с кодом, написанным на других языках, но необходимо вручную создать Dockerfile, прежде чем запускать `azds up` в первый раз.

Если приложение написано на языке, который Azure Dev Spaces не поддерживает встроенную поддержку, необходимо предоставить соответствующий Dockerfile для создания образа контейнера, выполняющего код. Необходимые сведения для написания файла Docker, который отвечает вашим потребностям, см. в статье с [рекомендациями по написанию файлов Docker](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) и справочнике по [файлам Docker](https://docs.docker.com/engine/reference/builder/).

Получив соответствующий Dockerfile, вы запускаете `azds up` для запуска приложения в Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Распространенные проблемы при запуске или остановке служб с помощью Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Ошибка "файл конфигурации не найден:"

При запуске `azds up`может появиться эта ошибка. `azds up` и `azds prep` должны запускаться из корневого каталога проекта, который требуется запустить в пространстве разработки.

Чтобы устранить эту проблему:
1. Измените текущий каталог на корневую папку, содержащую код службы. 
1. Если у вас нет файла _аздс. YAML_ в папке Code, запустите `azds prep` для создания DOCKER, Kubernetes и Azure dev Spaces ресурсов.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Время ожидания в "ожидании сборки образа контейнера..." Шаг с виртуальными узлами AKS

Это время ожидания возникает при попытке использовать пространства разработки для запуска службы, настроенной для запуска на [виртуальном узле AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). В настоящее время пространства разработки не поддерживают сборку или отладку служб на виртуальных узлах.

При запуске `azds up` с параметром `--verbose` или включении подробного ведения журнала в Visual Studio можно просмотреть дополнительные сведения:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Приведенная выше команда показывает, что модуль Pod службы был назначен *виртуальному узлу Virtual-node-ACI-Linux*, который является виртуальным узлом.

Чтобы устранить эту проблему, обновите диаграмму Helm для службы, чтобы удалить *все значения* *нодеселектор* или допусков, позволяющие службе работать на виртуальном узле. Обычно эти значения определяются в файле `values.yaml` диаграммы.

Вы по-прежнему можете использовать кластер AKS с включенной функцией виртуальных узлов, если служба, которую вы хотите создать или отладить через пространства разработки, выполняется на узле виртуальной машины. Запуск службы с пространствами разработки на узле виртуальной машины является конфигурацией по умолчанию.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Ошибка "не удалось найти готовый модуль кассового модуля" при запуске пространств разработки

Эта ошибка возникает, если клиент Helm больше не может обратиться к модулю pod Tiller, который выполняется в кластере.

Чтобы устранить эту проблему, перезапустите узлы агента в кластере.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Ошибка "Release аздс-\<идентификатор\>-\<спаценаме\>-\<ServiceName\> не пройден: службы"\<ServiceName\>"уже существует" или "отказано в доступе для \<ServiceName"\>, репозиторий не существует или может требовать "DOCKER Login"

Эти ошибки могут возникать при смешении выполнения команд Direct Helm (таких как `helm install`, `helm upgrade`или `helm delete`) с командами пространства разработки (например, `azds up` и `azds down`) внутри одного пространства разработки. Это происходит потому, что пространства разработки имеют собственный экземпляр ящика, который конфликтует с собственным экземпляром, работающим в том же пространстве разработки.

В одном кластере AKS можно использовать команды Helm и пространства разработки, но для каждого пространства имен, поддерживающего пространства разработки, должно использоваться одно или другое.

Например, предположим, что вы используете команду Helm для запуска всего приложения в родительском пространстве разработки. Можно создавать дочерние пространства разработки за пределами этого родителя, использовать пространства разработки для выполнения отдельных служб внутри дочерних пространств разработки и протестировать службы вместе. Когда вы будете готовы к возврату изменений, используйте команду Helm, чтобы развернуть обновленный код в родительском пространстве разработки. Не используйте `azds up` для запуска обновленной службы в родительском пространстве разработки, так как она будет конфликтовать со службой, изначально запущенной с помощью Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Существующие Dockerfile не используются для создания контейнера

Настройте службу Azure Dev Spaces так, чтобы она указывала на конкретный файл _Dockerfile_ в вашем проекте. Если Azure Dev Spaces не использует файл _Dockerfile_, который вы хотели использовать для сборки своих контейнеров, то можно явно указать Azure Dev Spaces, какой файл Dockerfile нужно использовать. 

Чтобы устранить эту проблему, откройте файл _аздс. YAML_ , который Azure dev Spaces создан в проекте. *Конфигурации обновления: Разработка: сборка: dockerfile* , указывающая на dockerfile, который вы хотите использовать. Пример.

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Ошибка "не авторизовано: требуется проверка подлинности" при попытке использовать образ DOCKER из частного реестра

Вы используете образ DOCKER из частного реестра, требующего проверки подлинности.

Чтобы устранить эту проблему, можно разрешить модулям разработки выполнять проверку подлинности и извлечение образов из этого частного реестра с помощью [имажепуллсекретс](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Чтобы использовать Имажепуллсекретс, [Создайте секрет Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) в пространстве имен, где вы используете образ. Затем укажите секрет в качестве Имажепуллсекрет в `azds.yaml`.

Ниже приведен пример указания Имажепуллсекретс в `azds.yaml`.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> Установка Имажепуллсекретс в `azds.yaml` приведет к переопределению Имажепуллсекретс, указанного в `values.yaml`.

### <a name="error-service-cannot-be-started"></a>Ошибка "не удается запустить службу".

Эта ошибка может возникать, когда не удается запустить код службы. Чаще всего причина в пользовательском коде. Чтобы получить дополнительные диагностические сведения, включите более подробное ведение журнала при запуске службы.

В командной строке используйте `--verbose`, чтобы включить более подробное ведение журнала. Можно также указать формат выходных данных с помощью `--output`. Пример.

```cmd
azds up --verbose --output json
```

В Visual Studio:

1. Откройте меню **Сервис > Параметры** и в разделе **Проекты и решения** выберите **Сборка и запуск**.
2. Измените значение параметра **Степень подробности сообщений при сборке проекта MSBuild** на **Подробно** или **Диагностика**.

    ![Снимок экрана диалогового окна "Сервис > Параметры"](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Повторный запуск службы после повторного создания контроллера

Ошибка *Не удается запустить службу* может появиться при попытке перезапустить службу после удаления и повторного создания контроллера Azure Dev Spaces, связанного с этим кластером. В этом случае подробный вывод содержит следующий текст:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Эта ошибка возникает из-за того, что при удалении контроллера dev Spaces не удаляются службы, ранее установленные этим контроллером. Воссоздание контроллера и последующая попытка запустить службы с использованием нового контроллера завершаются неудачно, поскольку старые службы все еще находятся на месте.

Чтобы решить эту проблему, используйте команду `kubectl delete`, чтобы вручную удалить старые службы из кластера, а затем повторно запустите Dev Spaces для установки новых служб.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Ошибка "не удается запустить службу". При использовании многоэтапного файлы dockerfile

Вы получите сообщение об ошибке *Не удается запустить службу* при использовании многоэтапного Dockerfile. В этом случае подробный вывод содержит следующий текст:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Эта ошибка возникает из-за того, что узлы AKS запускают более раннюю версию DOCKER, которая не поддерживает многоэтапные сборки. Чтобы избежать многоэтапных сборок, повторно создайте Dockerfile.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Распространенные проблемы с использованием Visual Studio и Visual Studio Code с Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Ошибка "отсутствуют необходимые инструменты и конфигурации"

Эта ошибка может возникать при запуске VS Code: "[Azure Dev Spaces] Отсутствуют необходимые средства и конфигурации для сборки и отладки "[имя проекта]"".
Ошибка означает, что это средство azds.exe не находится в переменной среды PATH, как показано в VS Code.

Попробуйте запустить VS Code из командной строки, где задана правильная переменная среды PATH.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Ошибка "Required tools to build and debug 'projectname' are out of date " ("Необходимые инструменты для сборки и отладки "projectname" устарели")

Вы увидите эту ошибку в Visual Studio Code, если у вас есть более новая версия расширения VS Code для Azure Dev Spaces, но более старая версия Azure Dev Spaces CLI.

Попробуйте загрузить и установить последнюю версию Azure Dev Spaces CLI:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Ошибка: "не удалось найти расширение отладчика для типа: CoreCLR"

Эта ошибка может появиться при запуске отладчика Visual Studio Code. Возможно, у вас нет расширения VS Code для C# установки на компьютере разработки. C# Расширение включает поддержку отладки для .NET Core (CoreCLR).

Чтобы устранить эту проблему, установите [расширение VS Code для C# ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Ошибка "настроенный тип отладки" CoreCLR "не поддерживается"

Эта ошибка может появиться при запуске отладчика Visual Studio Code. Возможно, у вас нет расширения VS Code для Azure Dev Spaces, установленного на компьютере разработки.

Чтобы устранить эту проблему, установите [расширение VS Code для Azure dev Spaces](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Ошибка "Недопустимое значение" КВД ""/src ". The system cannot find the file specified" ("Недопустимое значение "/src" каталога "cwd". Системе не удается найти указанный файл") или "launch: program '/src/[path to project binary]' does not exist" ("запуск: программа "/src/[путь к двоичному файлу проекта]" не существует")

Эта ошибка может появиться при запуске отладчика Visual Studio Code. По умолчанию расширение VS Code использует `src` в качестве рабочего каталога для проекта в контейнере. Эта ошибка может возникать, если вы обновили свой `Dockerfile`, указав другой рабочий каталог.

Чтобы устранить эту проблему, обновите файл `launch.json` в подкаталоге `.vscode` папки проекта. Измените каталог `configurations->cwd`, чтобы он указывал на тот же каталог, что и `WORKDIR`, определенный в `Dockerfile` проекта. Возможно, вам также потребуется обновить директиву `configurations->program`.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Ошибка "неожиданное завершение программы канала" аздс "с кодом 126".

Эта ошибка может появиться при запуске отладчика Visual Studio Code.

Чтобы устранить эту проблему, закройте и снова откройте Visual Studio Code. Перезапустите отладчик.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Ошибка "Сбой внутреннего наблюдения: Просмотр ЕНОСПК" при присоединении отладки к приложению Node. js

Эта ошибка возникает, когда узел, на котором выполняется модуль с приложением Node. js, к которому вы пытаетесь присоединиться с помощью отладчика, превысил значение *FS. инотифи. Max _user_watches* . В некоторых случаях [значение по умолчанию для *FS. инотифи. Max _user_watches* может быть слишком маленьким для параллельного подключения отладчика к Pod](https://github.com/Azure/AKS/issues/772).

Временное решение этой проблемы — увеличить значение *FS. инотифи. Max _user_watches* на каждом узле в кластере и перезапустить этот узел, чтобы изменения вступили в силу.

## <a name="other-common-issues"></a>Другие распространенные проблемы

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Ошибка "аздс" не распознана как внутренняя или внешняя команда, исполняемая программа или пакетный файл

Эта ошибка может произойти, если `azds.exe` не установлена или неправильно настроена.

Чтобы устранить эту проблему:

1. Проверьте расположение% ProgramFiles%/Microsoft Сдкс\азуре\азуре dev Spaces CLI для `azds.exe`. Если файл есть в этой папке, добавьте это расположение в переменную среды PATH.
2. Если `azds.exe` не установлен, выполните следующую команду:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Ошибка авторизации "Microsoft. Девспацес/Register/Action"

Вам потребуется доступ *владельца* или *участника* в подписке Azure, чтобы управлять Azure Dev Spaces. Если вы пытаетесь управлять пространствами разработки и у вас нет доступа *владельца* или *участника* к связанной подписке Azure, может появиться сообщение об ошибке авторизации. Пример.

```console
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Чтобы устранить эту проблему, с помощью учетной записи с *владельцем* или *участником* , обращающимся к подписке Azure, вручную Зарегистрируйте `Microsoft.DevSpaces` пространство имен:

```console
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Не запускаются новые модули

Инициализатор Kubernetes не может применить Подспек для новых модулей Pod из-за изменения разрешений RBAC для роли *администратора кластера* в кластере. Новый Pod также может иметь недопустимый Подспек, например учетную запись службы, связанную с этим модулем, больше не существует. Чтобы просмотреть модули, которые находятся в состоянии *ожидания* из-за проблемы инициализатора, используйте команду `kubectl get pods`.

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Эта проблема может повлиять на модули Pod во *всех пространствах имен* в кластере, включая пространства имен, в которых Azure dev Spaces не включена.

Чтобы устранить эту проблему, [Обновите интерфейс командной строки "пространства разработки" до последней версии](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) , а затем удалите *аздс инитиализерконфигуратион* из контроллера Azure dev Spaces.

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

После удаления *Аздс инитиализерконфигуратион* из контроллера Azure Dev Spaces используйте `kubectl delete`, чтобы удалить все модули из модулей в состоянии *ожидания* . После удаления всех ожидающих модулей Pod выполните повторное развертывание модулей.

Если новые модули Pod по-прежнему находятся в состоянии *ожидания* после повторного развертывания, используйте `kubectl delete`, чтобы удалить все модули из состояния *ожидания* . После удаления всех ожидающих модулей Pod удалите контроллер из кластера и переустановите его:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

После переустановки контроллера выполните повторное развертывание модулей Pod.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Неверные разрешения RBAC для вызова контроллера и интерфейсов API пространства разработки

Пользователь, обращающийся к контроллеру Azure Dev Spaces, должен иметь доступ для чтения *kubeconfig* администратора в кластере AKS. Например, это разрешение доступно во [встроенной роли администратора кластера службы Kubernetes Azure](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). Пользователь, обращающийся к контроллеру Azure Dev Spaces, должен также иметь роль *участника* или *владельца* RBAC для контроллера. Дополнительные сведения об обновлении разрешений пользователя для кластера AKS доступны [здесь](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Чтобы обновить роль RBAC пользователя для контроллера, выполните следующие действия.

1. Войдите на портал Azure по адресу https://portal.azure.com.
1. Перейдите к группе ресурсов, содержащей контроллер, который обычно совпадает с кластером AKS.
1. Установите флажок *Показывать скрытые типы* .
1. Щелкните контроллер.
1. Откройте панель *управления доступом (IAM)* .
1. Перейдите на вкладку *назначения ролей* .
1. Щелкните *Добавить* , а затем *добавить назначение ролей*.
    * В качестве *роли*выберите *участник* или *владелец*.
    * В поле *Назначение доступа к* выберите *Пользователь, группа или субъект-служба Azure AD*.
    * В поле *выбрать*найдите пользователя, которого нужно предоставить разрешения.
1. В нижней части страницы нажмите кнопку *Save*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Сбой разрешения DNS-имен для общедоступных URL-адресов, связанных со службой Dev Spaces

Вы можете настроить общедоступную конечную точку URL-адреса для службы, указав параметр `--public` в команде `azds prep` или установив флажок `Publicly Accessible` в Visual Studio. Общедоступное DNS-имя автоматически регистрируется при запуске службы в Dev Spaces. Если DNS-имя не зарегистрировано, в веб-браузере во время подключения к общедоступному URL-адресу вы увидите ошибки *Не удается отобразить страницу* или *Этот сайт недоступен*.

Чтобы устранить эту проблему:

* Проверьте состояние всех URL-адресов, связанных со службами пространства разработки:

  ```console
  azds list-uris
  ```

* Если URL-адрес находится в состоянии *ожидания* , то пространства разработки по-прежнему ожидают завершения регистрации DNS. Иногда для выполнения регистрации требуется несколько минут. Для каждой службы Dev Spaces также открывается туннель localhost, который можно использовать во время ожидания регистрации DNS.
* Если URL-адрес остается в состоянии *Ожидание* более 5 минут, это может указывать на проблему с внешним модулем DNS pod, который создает общедоступную конечную точку или контроллер nginx-входа, который приобретает общедоступную конечную точку. Используйте следующие команды, чтобы удалить эти модули и позволить AKS автоматически воссоздать их:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Ошибка "ошибка вышестоящего подключения или отключение/сброс перед заголовками"

Вы можете увидеть эту ошибку при попытке получить доступ к службе. Например, при переходе по URL-адресу службы в браузере. Эта ошибка означает, что порт контейнера недоступен. Это может быть вызвано следующими причинами.

* Процесс сборки или развертывания контейнера еще не завершен. Это может случиться, если вы запускаете `azds up` или отладчик, а затем пытаетесь получить доступ к контейнеру до его успешного развертывания.
* Конфигурация порта не согласуется в _Dockerfile_, диаграмме Helm и серверном коде, который открывает порт.

Чтобы устранить эту проблему:

1. Если процесс сборки или развертывания еще не завершен, вы можете подождать 2–3 секунды и повторить попытку доступа к службе. 
1. Проверьте конфигурацию порта. Указанные номера портов должны **совпадать** во всех следующих ресурсах:
    * **Dockerfile:** задается инструкцией `EXPOSE`.
    * **[Диаграмма Helm](https://docs.helm.sh):** задается значениями `externalPort` и `internalPort` для службы (часто находится в файле `values.yml`).
    * Все порты, открытые в коде приложения, например в Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Не удалось найти тип или имя пространства имен "MyLibrary"

Не удается найти проект библиотеки, который вы используете. В пространствах разработки контекст сборки по умолчанию находится на уровне проекта или службы.  

Чтобы устранить эту проблему:

1. Измените файл `azds.yaml`, чтобы задать для контекста сборки уровень решения.
2. Измените файлы `Dockerfile` и `Dockerfile.develop`, чтобы они ссылались на файлы проекта, например `.csproj`, правильно относительные по отношению к новому контексту сборки.
3. Добавьте `.dockerignore` в тот же каталог, где находится файл `.sln`.
4. При необходимости обновите `.dockerignore` дополнительными записями.

Пример можно найти [здесь](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Горизонтальное Автомасштабирование Pod не работает в пространстве разработки

При запуске службы в пространстве разработки этот модуль служб [внедряется в дополнительные контейнеры для инструментирования](how-dev-spaces-works.md#prepare-your-aks-cluster) , а все контейнеры в Pod должны иметь ограничения ресурсов и запросы, настроенные для горизонтального автомасштабирования Pod.

Чтобы устранить эту проблему, примените запрос ресурса и ограничьте его до контейнеров с внедренными пространствами разработки. Запросы и ограничения ресурсов могут применяться к внедренному контейнеру (девспацес-proxy) путем добавления заметки `azds.io/proxy-resources` в спецификацию Pod. Значение должно быть задано для объекта JSON, представляющего раздел Resources в спецификации контейнера для учетной записи-посредника.

Ниже приведен пример Аннотации "прокси-ресурсы", которая будет применяться к спецификации Pod.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Включение Azure Dev Spaces в существующем пространстве имен с выполняющимися модулями

У вас может быть существующий кластер AKS и пространство имен с работающими модулями, в которых необходимо включить Azure Dev Spaces.

Чтобы включить Azure Dev Spaces в существующем пространстве имен в кластере AKS, запустите `use-dev-spaces` и используйте `kubectl` для перезапуска всех модулей Pod в этом пространстве имен.

```console
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
kubectl -n my-namespace delete pod --all
```

После перезапуска модулей можно начать использовать существующее пространство имен с Azure Dev Spaces.