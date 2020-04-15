---
title: Устранение неполадок
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Узнайте, как устранить неполадки и решить общие проблемы при включении и использовании пространства Azure Dev
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: 9fcf14bf42fc843a126fea269038087ee7fb0c6c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382045"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Устранение неполадок Azure Dev Spaces

Это руководство содержит сведения о распространенных проблемах, которые могут возникнуть при использовании Azure Dev Spaces.

Если у вас возникли проблемы при использовании azure Dev Spaces, создайте [проблему в репозитории Azure Dev Spaces GitHub.](https://github.com/Azure/dev-spaces/issues)

## <a name="before-you-begin"></a>Перед началом

Чтобы более эффективно устранять неполадки, можно создать более подробные журналы для проверки.

Для расширения Visual Studio задайте переменной среды `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` значение 1. Обязательно перезапустите Visual Studio, чтобы изменения переменной среды вступили в силу. После включения подробные журналы будут записаны в каталог `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

В CLI вы можете выводить больше сведений во время выполнения команды, используя параметр `--verbose`. Вы также можете просмотреть более подробные журналы в `%TEMP%\Azure Dev Spaces`. На Mac каталог *TEMP* можно найти, `echo $TMPDIR` выбежав из окна терминала. На компьютере Linux, каталог *TEMP,* как правило, `/tmp`. Кроме того, убедитесь, что журнал включен в [файл конфигурации Azure CLI.](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables)

Пространства Azure Dev также лучше всего работают при отладке одного экземпляра или стручка. Файл `azds.yaml` содержит *параметр, replicaCount*, который указывает количество стручков, которые Kubernetes запускает для вашей службы. Если вы измените *реплику Count,* чтобы настроить приложение для выполнения нескольких стручков для данной службы, отладчик прикрепляется к первому стручку, если он указан в алфавитном порядке. Отладчик присоединяется к другому модулю pod при перезапуске оригинального модуля pod, что может привести к непредвиденному поведению.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Общие проблемы при включении пространства Azure Dev

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Ошибка "Не удалось создать контроллер Azure Dev Spaces"

Вы можете увидеть эту ошибку, если что-то пойдет не так с созданием контроллера. Если это временная ошибка, ее можно устранить, удалив контроллер, а затем повторно создав его.

Вы также можете попробовать удалять контроллер:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Для удаления контроллера используйте CLI Azure Dev Spaces. Удалить контроллер из Visual Studio невозможно. Также нельзя устанавливать CLI Azure Dev Spaces в оболочку Azure Cloud Shell, поэтому вы не можете удалить контроллер из облачной оболочки Azure.

Если у вас нет установлена CLI Azure Dev Spaces, вы можете сначала установить его с помощью следующей команды, а затем удалить контроллер:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Чтобы повторно создать контроллер, можно использовать CLI или Visual Studio. Например, ознакомиться с [разработкой группы](quickstart-team-development.md) или [разработать с помощью quickstarts .NET Core.](quickstart-netcore-visualstudio.md)

### <a name="controller-create-failing-because-of-controller-name-length"></a>Контроллер создать сбой из-за длины имени контроллера

Имя контроллера Azure Dev Spaces не может быть длиннее 31 символа. Если имя контроллера превышает 31 символ, когда вы включите Dev Spaces в кластере AKS или создадите контроллер, вы получите ошибку. Пример:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Чтобы исправить эту проблему, создайте контроллер с альтернативным именем. Пример:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Включение Dev Spaces сбой при добавлении пулов узлов Windows в кластер AKS

В настоящее время Azure Dev Spaces предназначен айпна только на стручках и узлах Linux. При кластере AKS с пулом узлов Windows необходимо убедиться, что стручки Azure Dev Spaces запланированы только на узлах Linux. Если стручок Azure Dev Spaces планируется запустить на узеле Windows, этот модуль не запустится, и включение Dev Spaces выйдет из строя.

Чтобы устранить эту проблему, [добавьте запятнание](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) в кластер AKS, чтобы убедиться, что стручки Linux не будут запущены на узлах Windows.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Ошибка "Не найдено незапятнанных узлов Linux в состоянии готовности в кластере. Для развертывания стручков в пространстве имен «azds» должен быть по крайней мере один незапятнанный узел Linux.

Пространства Azure Dev не смогли создать контроллер в кластере AKS, так как не смогли найти незапятнанный узел в *готовом* состоянии для планирования стручков. Пространства Azure Dev требуют по крайней мере одного узла Linux в состоянии *готовности,* который позволяет планировать стручки без указания терпимого положения.

Чтобы устранить эту проблему, [обновите конфигурацию загрязнения](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) в кластере AKS, чтобы обеспечить, по крайней мере, один узла Linux позволяет планировать стручки без указания терпимостей. Кроме того, убедитесь, что по крайней мере один узло Linux, который позволяет планировать стручки без указания толерантности находится в состоянии *готовности.* Если ваш узла занимает много времени, чтобы достичь состояния *Ready,* вы можете попробовать перезапустить свой узла.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Ошибка "Azure Dev Spaces CLI не установлена должным образом" при запуске az aks использования-dev-пространства

Обновление CLI Azure Dev Spaces изменило траекторию установки. Если вы используете версию Azure CLI раньше 2.0.63, вы можете увидеть эту ошибку. Для отображения версии Azure CLI используйте: `az --version`

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Несмотря на сообщение `az aks use-dev-spaces` об ошибке при запуске с версией Azure CLI до 2.0.63, установка действительно удалась. Вы можете продолжать `azds` использовать без каких-либо проблем.

Чтобы устранить эту проблему, обновите установку [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) до 2.0.63 или позже. Это обновление позволит разрешить сообщение об `az aks use-dev-spaces`ошибке, полученное при запуске. Кроме того, можно продолжить использование текущей версии CLI Azure и CLI Azure Dev Spaces.

### <a name="error-unable-to-reach-kube-apiserver"></a>Ошибка "Не удается достичь kube-apiserver"

Эта ошибка может быть допущена, когда Пробелы Azure Dev не могут подключиться к серверу API кластера AKS.

Если доступ к серверу API кластера AKS заблокирован или если для кластера AKS [включены диапазоны авторизованных IP-адресов сервера API,](../aks/api-server-authorized-ip-ranges.md) необходимо также [создать](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) или [обновить](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) кластер, чтобы [обеспечить дополнительные диапазоны в зависимости от региона.](https://github.com/Azure/dev-spaces/tree/master/public-ips)

Убедитесь, что сервер API доступен при запуске команд kubectl. Если сервер API недоступен, пожалуйста, свяжитесь с службой поддержки AKS и повторите попытку при работе сервера API.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Общие проблемы при подготовке проекта для Пространства Azure Dev

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Предупреждение "Dockerfile не может быть создан из-за неподдерживаемого языка"
Azure Dev Spaces предоставляет встроенную поддержку C# и Node.js. При запуске `azds prep` в каталоге с кодом, написанным на одном из этих языков, Azure Dev Spaces автоматически создает для вас подходящий Dockerfile.

Вы все еще можете использовать Пространства Azure Dev с кодом, написанным на `azds up` других языках, но вам нужно вручную создать Dockerfile перед запуском в первый раз.

Если приложение написано на языке, который Azure Dev Spaces не поддерживает на родном уровне, необходимо предоставить соответствующий Dockerfile для создания изображения контейнера под управлением вашего кода. Необходимые сведения для написания файла Docker, который отвечает вашим потребностям, см. в статье с [рекомендациями по написанию файлов Docker](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) и справочнике по [файлам Docker](https://docs.docker.com/engine/reference/builder/).

После того, как у вас есть `azds up` соответствующий Dockerfile на месте, вы запустите для запуска приложения в Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Общие проблемы при запуске или остановке служб с Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Ошибка "Файл Config не найден:"

При `azds up`запуске вы можете увидеть эту ошибку. Оба `azds up` `azds prep` и должны быть запущены из корневого каталога проекта, который вы хотите запустить в вашем пространстве разработчиков.

Чтобы устранить эту проблему:
1. Измените текущий каталог на корневую папку, содержащую код службы. 
1. Если в папке кода нет файла _azds.yaml,_ запустите `azds prep` для генерации ресурсов Docker, Kubernetes и Azure Dev Spaces.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Тайм-аут в "Ожидание создания изображения контейнера..." шаг с акС виртуальные узлы

Этот тайм-аут происходит при попытке использовать Dev Spaces для запуска службы, настроенной для запуска на [виртуальном узоте AKS.](https://docs.microsoft.com/azure/aks/virtual-nodes-portal) Dev Spaces в настоящее время не поддерживает службы построения или отладки виртуальных узлов.

При запуске `azds up` с параметром `--verbose` или включении подробного ведения журнала в Visual Studio можно просмотреть дополнительные сведения:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Вышеупомянутая команда показывает, что стручок службы был назначен *виртуальному узлам-аци-linux,* который является виртуальным узлам.

Чтобы исправить эту проблему, обновите диаграмму Helm для службы, чтобы удалить любые значения *узлового или* *терпимого* значения, которые позволяют службе работать на виртуальном узлах. Обычно эти значения определяются в файле `values.yaml` диаграммы.

Вы все еще можете использовать кластер AKS с включенной функцией виртуальных узлов, если служба, которую вы хотите построить или отладить через Dev Spaces, работает на vM-узле. Запуск службы с Dev Spaces на vM-узоте — это конфигурация по умолчанию.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Ошибка "не удалось найти готовый стручок рулера" при запуске Dev Spaces

Эта ошибка возникает, если клиент Helm больше не может обратиться к модулю pod Tiller, который выполняется в кластере.

Чтобы устранить эту проблему, перезапустите узлы агента в кластере.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Ошибка "релиз\<azds-\>-\<идентификатор spacename\>-\<servicename не\> удалось: сервисы \<'\>\<имя\>службы 'уже существует" или "Pull доступ отказано для servicename , репозиторий не существует или может потребовать 'Docker Login'"

Эти ошибки могут возникнуть, если вы `helm install`смешиваете запуск исправных команд Helm (например, `helm upgrade`, или) `helm delete`с командами Dev Spaces (например, `azds up` и) `azds down`внутри одного и того же пространства разработчиков. Они происходят потому, что Dev Spaces имеет свой собственный экземпляр Tiller, который вступает в конфликт с вашим собственным экземпляром Tiller, работающим в том же пространстве разработчиков.

Это нормально использовать обе команды Helm и Dev Spaces команды против того же кластера AKS, но каждый Dev Spaces включен ы имя должно использовать один или другой.

Например, предположим, что вы используете команду Helm для запуска всего приложения в пространстве родительских разработчиков. Можно создать пробелы для разработчиков детей от этого родителя, использовать Dev Spaces для выполнения отдельных служб внутри пространства для разработчиков ребенка и совместно протестировать службы. Когда вы будете готовы проверить изменения, используйте команду Helm для развертывания обновленного кода в пространстве родительского разработчика. Не используйте `azds up` для запуска обновленной службы в пространстве родительского разработчика, так как это будет противоречить службе, первоначально запущенной с помощью Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Существующий Dockerfile не используется для создания контейнера

Пространства Azure Dev могут быть настроены для указать на конкретный _Dockerfile_ в проекте. Если Azure Dev Spaces не использует файл _Dockerfile_, который вы хотели использовать для сборки своих контейнеров, то можно явно указать Azure Dev Spaces, какой файл Dockerfile нужно использовать. 

Чтобы устранить эту проблему, откройте файл _azds.yaml,_ созданный в проекте. Конфигурации *обновления: разработка: сборка: докерфайл,* чтобы указать на Dockerfile, который вы хотите использовать. Пример:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Ошибка "несанкционированная: требуется аутентификация" при попытке использования изображения Docker из частного реестра

Вы используете изображение Docker из частного реестра, которое требует проверки подлинности.

Чтобы исправить эту проблему, вы можете позволить Dev Spaces для проверки подлинности и вытащить изображения из этого частного реестра с помощью [imagePullSecrets.](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets) Чтобы использовать imagePullSecrets, [создайте секрет Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) в пространстве имен, где вы используете изображение. Затем предоставьте секрет в виде `azds.yaml`изображенияPullSecret в .

Ниже приведен пример указанного изображенияPullSecrets `azds.yaml`в .

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
> Установка imagePullSecrets `azds.yaml` в переопределяет изображениеPullSecrets, указанный `values.yaml`в .

### <a name="error-service-cannot-be-started"></a>Ошибка "Служба не может быть запущена".

Эта ошибка может возникать, когда не удается запустить код службы. Чаще всего причина в пользовательском коде. Чтобы получить больше диагностической информации, включите более подробную систему регистрации при запуске службы.

Из командной строки `--verbose` используйте систему более подробной регистрации. Вы также можете указать `--output`формат вывода с помощью . Пример:

```cmd
azds up --verbose --output json
```

В Visual Studio:

1. Откройте меню **Сервис > Параметры** и в разделе **Проекты и решения** выберите **Сборка и запуск**.
2. Измените значение параметра **Степень подробности сообщений при сборке проекта MSBuild** на **Подробно** или **Диагностика**.

    ![Снимок экрана диалогового окна "Сервис > Параметры"](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Повторный запуск службы после повторного создания контроллера

Ошибка *Не удается запустить службу* может появиться при попытке перезапустить службу после удаления и повторного создания контроллера Azure Dev Spaces, связанного с этим кластером. В этом случае подробный вывод содержит следующий текст:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Эта ошибка возникает из-за удаления контроллера Dev Spaces, не удаляя службы, ранее установленные этим контроллером. Воссоздание контроллера и последующая попытка запустить службы с использованием нового контроллера завершаются неудачно, поскольку старые службы все еще находятся на месте.

Чтобы решить эту проблему, используйте команду `kubectl delete`, чтобы вручную удалить старые службы из кластера, а затем повторно запустите Dev Spaces для установки новых служб.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Ошибка "Служба не может быть запущена". при использовании многоступенчатых Dockerfiles

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

Эта ошибка возникает из-за того, что пространства Azure Dev в настоящее время не поддерживают многоступенчатые сборки. Чтобы избежать многоэтапных сборок, повторно создайте Dockerfile.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>Сетевой трафик не перенаправляется в кластер AKS при подключении машины разработки

При использовании [Azure Dev Spaces для подключения кластера AKS к машине разработки](how-to/connect.md)вы можете столкнуться с проблемой, когда сетевой трафик не перенаправляется между машиной разработки и кластером AKS.

При подключении машины разработки к кластеру AKS Azure Dev Spaces перенаправляет сетевой трафик между кластером AKS и машиной разработки, модифицируя `hosts` файл машины разработки. Пространства Azure Dev создают `hosts` запись в адресе службы Kubernetes, который вы заменяете в качестве имени хоста. Эта запись используется с переадресировкой порта для прямого сетевого трафика между машиной разработки и кластером AKS. Если служба в вашей машине разработки противоречит заменяемому вам порту службы Kubernetes, Azure Dev Spaces не может пересылать сетевой трафик для службы Kubernetes. Например, служба *Windows BranchCache* обычно привязана к *0.0.0.0.80*, что приводит к конфликту для порта 80 на всех локальных ис-центрах.

Чтобы исправить эту проблему, необходимо прекратить любые службы или процессы, которые противоречат порту сервиса Kubernetes, который вы пытаетесь заменить. Вы можете использовать инструменты, такие как *netstat,* чтобы проверить, какие службы или процессы на вашей машине разработки находятся в конфликте.

Например, чтобы остановить и отключить службу *Windows BranchCache:*
* Выполнить `services.msc` от команды запроса.
* Нажмите на *BranchCache* и выберите *Свойства*.
* Нажмите *Остановить*.
* Дополнительно вы можете отключить его, установив *тип запуска* для *инвалидов.*
* Нажмите кнопку *ОК*.

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>Ошибка "нет AzureAssignedIdentity найдено для стручка:azds/azds-webhook-развертывание-id\<\> в назначенном состоянии"

При запуске службы с Azure Dev Spaces в кластере AKS с установленными [управляемыми идентификаторами и](../aks/use-managed-identity.md) [идентификационными данными стручка](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) может зависнуть после шага *установки диаграммы.* Если вы проинспектируете *azds-injector-webhook* в пространстве имени *azds,* вы можете увидеть эту ошибку.

Службы Azure Dev Spaces, запускаемые в кластере, используют управляемый идентатор кластера для связи с бэкэнд-сервисами Azure Dev Spaces за пределами кластера. При установке управляемого идентификатора стручка сетевые правила настраиваются на узлы кластера для перенаправления всех вызовов управляемых учетных данных на [установленный на кластере управляемый identity forii (NMI) DaemonSet.](https://github.com/Azure/aad-pod-identity#node-managed-identity) Это NMI DaemonSet определяет вызов стручка и гарантирует, что стручок был помечен надлежащим образом для доступа к запрошенной управляемой идентификации. Пространства Azure Dev не могут определить, установлен ли кластер управляемым идентификатором и не может выполнить необходимую конфигурацию, чтобы позволить службам Azure Dev Spaces получить доступ к управляемому идентификатору кластера. Поскольку службы Azure Dev Spaces не настроены для доступа к управляемому идентификатору кластера, NMI DaemonSet не позволит им получить токен AAD для управляемого идентификатора и не сможет связаться с бэкэнд-сервисами Azure Dev Spaces.

Чтобы устранить эту проблему, примените [AzurePodIdentityException](https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md) для *azds-injector-webhook* и обновите стручки, спомощьив Azure Dev Spaces, для доступа к управляемому идентификатору.

Создайте файл под названием *webhookException.yaml* и скопируйте следующее определение YAML:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: azds
spec:
  PodLabels:
    azds.io/uses-cluster-identity: "true"
```

Вышеуказанный файл создает объект *AzurePodIdentityException* для *azds-injector-webhook.* Для развертывания этого `kubectl`объекта используйте:

```cmd
kubectl apply -f webhookException.yaml
```

Чтобы обновить стручки, инструментированные Azure Dev Spaces для доступа к управляемому идентификатору, обновите *пространство имен* в приведенном ниже определении YAML и используйте `kubectl` для его применения для каждого пространства разработчиков.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: myNamespace
spec:
  PodLabels:
    azds.io/instrumented: "true"
```

Кроме того, можно создать объекты *AzureIdentity* и *AzureIdentityBinding* и обновить метки стручков для рабочих нагрузок, работающих в пространствах, управляемых пространствами Azure Dev spaces, для доступа к управляемому идентификатору, созданному кластером AKS.

Чтобы перечислить сведения об управляемом интактируется, запустите следующую команду для кластера AKS:

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

Вышеупомянутая команда выводит *clientId* и *resourceId* для управляемого удостоверения личности. Пример:

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

Чтобы создать объект *AzureIdentity,* создайте файл под названием *clusteridentity.yaml* и используйте следующее определение YAML, дополненное деталями управляемой идентификации из предыдущей команды:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentity
metadata:
  name: my-cluster-mi
spec:
  type: 0
  ResourceID: /subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>
  ClientID: <clientId>
```

Для создания объекта *AzureIdentityBinding* создайте файл под названием *clusteridentitybinding.yaml* и используйте следующее определение YAML:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

Для развертывания объектов *AzureIdentity* и *AzureIdentityBinding* и используйте: `kubectl`

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

После развертывания объектов *AzureIdentity* и *AzureIdentityBinding* любая рабочая нагрузка с *aadpodidbinding:* метка значения моей метки может получить доступ к управляемому идентификатору кластера. Добавьте эту метку и передислоците все рабочие нагрузки, работающие в любом пространстве разработчиков. Пример:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: sample
        aadpodidbinding: my-label-value
    spec:
      [...]
```

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Общие проблемы с использованием Visual Studio и visual Studio Code с Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Ошибка "Обязательные инструменты и конфигурации отсутствуют"

Эта ошибка может возникать при запуске VS Code: "[Azure Dev Spaces] Отсутствуют необходимые средства и конфигурации для сборки и отладки "[имя проекта]"".
Ошибка означает, что это средство azds.exe не находится в переменной среды PATH, как показано в VS Code.

Попробуйте запустить VS Code из командного запроса, где правильно настроена переменная среды PATH.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Ошибка "Required tools to build and debug 'projectname' are out of date " ("Необходимые инструменты для сборки и отладки "projectname" устарели")

Вы увидите эту ошибку в Visual Studio Code, если у вас есть более новая версия расширения VS Code для Azure Dev Spaces, но более старая версия Azure Dev Spaces CLI.

Попробуйте загрузить и установить последнюю версию CLI Azure Dev Spaces:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Ошибка: "Не удалось найти расширение отладки для типа:coreclr"

Вы можете увидеть эту ошибку при запуске отладчика visual Studio Code. Возможно, на машине разработки не будет установлено расширение VS-кода для C-кода. Расширение C's включает в себя отладку поддержки .NET Core (CoreCLR).

Чтобы устранить эту проблему, установите [расширение VS Code для C .](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Ошибка "Настроенный отладка типа 'coreclr' не поддерживается"

Вы можете увидеть эту ошибку при запуске отладчика visual Studio Code. Возможно, на машине разработки не будет установлено расширение VS Code для Azure Dev Spaces.

Чтобы устранить эту проблему, установите [расширение VS Code для Azure Dev Spaces.](get-started-netcore.md)

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Ошибка "Недействительное значение 'cwd' '/src'. The system cannot find the file specified" ("Недопустимое значение "/src" каталога "cwd". Системе не удается найти указанный файл") или "launch: program '/src/[path to project binary]' does not exist" ("запуск: программа "/src/[путь к двоичному файлу проекта]" не существует")

Вы можете увидеть эту ошибку при запуске отладчика visual Studio Code. По умолчанию расширение VS Code использует `src` в качестве рабочего каталога для проекта в контейнере. Эта ошибка может возникать, если вы обновили свой `Dockerfile`, указав другой рабочий каталог.

Чтобы устранить эту `launch.json` проблему, `.vscode` обновите файл под субкаталогификатором папки проекта. Измените каталог `configurations->cwd`, чтобы он указывал на тот же каталог, что и `WORKDIR`, определенный в `Dockerfile` проекта. Возможно, вам также потребуется обновить директиву `configurations->program`.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Ошибка "Программа труб "azds" неожиданно вышла с кодом 126".

Вы можете увидеть эту ошибку при запуске отладчика visual Studio Code.

Чтобы исправить эту проблему, закройте и вновь откройте Visual Studio Code. Перезапустите отладчик.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Ошибка "Внутренние часы не удалось: смотреть ENOSPC" при подключении отладки к приложению Node.js

Эта ошибка возникает, когда узла, работая в стручоксе с приложением Node.js, к которому вы пытаетесь прикрепить с помощью отладчика, превысило значение *fs.inotify.max_user_watches.* В некоторых случаях [значение *fs.inotify.max_user_watches* по умолчанию может быть слишком маленьким для обработки крепления отладчика непосредственно к стручке.](https://github.com/Azure/AKS/issues/772)

Временным решением этой проблемы является увеличение значения *fs.inotify.max_user_watches* на каждом узлах кластера и перезапуск этого узла для вхучивания изменений.

## <a name="other-common-issues"></a>Другие распространенные проблемы

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Ошибка "azds" не признается как внутренняя или внешняя команда, оперативная программа или пакетный файл

Эта ошибка может `azds.exe` произойти, если она не установлена или настроена правильно.

Чтобы устранить эту проблему:

1. Проверьте местоположение %ProgramFiles%/Microsoft SDKs-Azure-Azure Dev `azds.exe`Spaces CLI для. Если файл есть в этой папке, добавьте это расположение в переменную среды PATH.
2. Если `azds.exe` не установлен, запустите следующую команду:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Ошибка авторизации "Microsoft.DevSpaces/регистрация/действие"

Вам потребуется доступ *владельца* или *участника* в подписке Azure, чтобы управлять Azure Dev Spaces. Если вы пытаетесь управлять Dev Spaces и у вас нет доступа *владельца* или *автора* к связанной подписке Azure, вы можете увидеть ошибку авторизации. Пример:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Чтобы исправить эту проблему, используя учетную запись с *доступом владельца* `Microsoft.DevSpaces` или *автора* к подписке Azure, вручную зарегистрируйте пространство имен:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Новые стручки не начинаются

Инициатор Kubernetes не может применять PodSpec для новых стручков из-за изменений разрешения RBAC в роль *кластера-админ* в кластере. Новый модуль может также иметь недействительный PodSpec, например учетная запись службы, связанная с стручок больше не существует. Чтобы увидеть стручки, находящихся в состоянии *ожидания* из-за проблемы инициализатора, используйте `kubectl get pods` команду:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Эта проблема может повлиять на стручки во *всех пространствах имен* в кластере, включая области имен, где пробелы Azure Dev не включены.

Чтобы исправить эту проблему, [обновите CLI Dev Spaces до последней версии,](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) а затем удалите *azds InitializerConfiguration* из контроллера Azure Dev Spaces:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

После удаления *azds InitializerConfiguration* из контроллера Azure `kubectl delete` Dev Spaces используйте для удаления любых стручков в состоянии *ожидания.* После того, как все ожидающие стручки были удалены, передислоцировать ваши стручки.

Если новые стручки все *Pending* еще застряли в `kubectl delete` состоянии ожидания после передислокации, используйте для удаления любых стручков в состоянии *ожидания.* После того, как все ожидающие стручки были удалены, удалите контроллер из кластера и переустановите его:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

После того, как контроллер будет переустановлен, передислоцируйте стручки.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Неправильные разрешения RBAC для вызова контроллера Dev Spaces и AI

Пользователь, имеющий доступ к контроллеру Azure Dev Spaces, должен иметь доступ к чтению интерфейса *kubeconfig* в кластере AKS. Например, это разрешение доступно во [встроенной роли admin-сервиса Azure Kubernetes.](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions) Пользователь, имеющий доступ к контроллеру Azure Dev Spaces, также должен иметь роль *вкладчика* или *владельца* RBAC для контроллера. Более подробная информация об обновлении разрешений пользователя для кластера AKS доступна [здесь](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Чтобы обновить роль пользователя RBAC для контроллера:

1. Войдите на портал Azure по адресу https://portal.azure.com.
1. Перейдите к группе ресурсов, содержащей контроллер, который обычно совпадает с кластером AKS.
1. Включить *показать скрытые типы* флажок.
1. Нажмите на контроллер.
1. Откройте панель *управления доступом (IAM).*
1. Нажмите на вкладку *«Назначения ролей».*
1. Нажмите *Добавить* затем *Добавить назначение ролей*.
    * Для *роли*выберите *участника* или *владельца.*
    * В поле *Назначение доступа к* выберите *Пользователь, группа или субъект-служба Azure AD*.
    * Для *выбора*, поиск пользователя вы хотите дать разрешения.
1. Выберите команду *Сохранить*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Сбой разрешения DNS-имен для общедоступных URL-адресов, связанных со службой Dev Spaces

Вы можете настроить общедоступную конечную точку URL-адреса для службы, указав параметр `--enable-ingress` в команде `azds prep` или установив флажок `Publicly Accessible` в Visual Studio. Общедоступное DNS-имя автоматически регистрируется при запуске службы в Dev Spaces. Если DNS-имя не зарегистрировано, в веб-браузере во время подключения к общедоступному URL-адресу вы увидите ошибки *Не удается отобразить страницу* или *Этот сайт недоступен*.

Чтобы устранить эту проблему:

* Проверьте состояние всех URL-адресов, связанных с вашими службами Dev Spaces:

  ```console
  azds list-uris
  ```

* Если URL-адрес находится в состоянии *ожидания,* Dev Spaces все еще ждет завершения регистрации DNS. Иногда для выполнения регистрации требуется несколько минут. Для каждой службы Dev Spaces также открывается туннель localhost, который можно использовать во время ожидания регистрации DNS.
* Если URL-адрес остается в состоянии *Ожидание* более 5 минут, это может указывать на проблему с внешним модулем DNS pod, который создает общедоступную конечную точку или контроллер nginx-входа, который приобретает общедоступную конечную точку. Используйте следующие команды, чтобы удалить эти стручки и позволить AKS автоматически воссоздавать их:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Ошибка "ошибка подключения вверх по течению или отключение/ сбросить перед заголовками"

Вы можете увидеть эту ошибку при попытке получить доступ к службе. Например, при переходе по URL-адресу службы в браузере. Эта ошибка означает, что контейнерный порт недоступен. Это может по следующим причинам:

* Процесс сборки или развертывания контейнера еще не завершен. Это может случиться, если вы запускаете `azds up` или отладчик, а затем пытаетесь получить доступ к контейнеру до его успешного развертывания.
* Конфигурация порта не соответствует _вашему Dockerfile,_ Helm Chart и любому серверному коду, открывающий порт.

Чтобы устранить эту проблему:

1. Если процесс сборки или развертывания еще не завершен, вы можете подождать 2–3 секунды и повторить попытку доступа к службе. 
1. Проверьте конфигурацию порта в следующих активах:
    * **[Диаграмма руля](https://docs.helm.sh)** `service.port` : `deployment.containerPort` Определяется и в значениях.yaml леса по `azds prep` команде.
    * Все порты, открытые в коде приложения, например в Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Тип или имя пространства "MyLibrary" не может быть найдено

Проект библиотеки, который вы используете, не может быть найден. С Dev Spaces контекст сборки находится на уровне проекта/сервиса по умолчанию.  

Чтобы устранить эту проблему:

1. Измените `azds.yaml` файл, чтобы настроить контекст сборки до уровня решения.
2. Измените `Dockerfile` `Dockerfile.develop` и файлы для обозначения `.csproj`файлов проекта, например, правильно по отношению к новому контексту сборки.
3. Добавьте `.dockerignore` в тот же `.sln` каталог, что и файл.
4. Обновление `.dockerignore` с дополнительными входами по мере необходимости.

Вы можете найти пример [здесь](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Горизонтальный стручок автомасштабирования не работает в пространстве разработчиков

При запуске службы в пространстве разработчиков, что стручок [службы вводится с дополнительными контейнерами для приборов](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) и все контейнеры в стручке должны иметь ограничения ресурсов и запросы, установленные для Horizontal Pod Autoscaling.

Чтобы устранить эту проблему, примените запрос ресурса и ограничьте вводимые контейнеры Dev Spaces. Запросы и ограничения ресурсов могут быть применены для впрыскиваемого контейнера (devspaces-proxy) путем добавления `azds.io/proxy-resources` аннотации к спецификации стручка. Значение должно быть установлено на объект jSON, представляющий раздел ресурсов спецификации контейнера для прокси.

Ниже приведен пример аннотации прокси-ресурсов, которая должна быть применена к вашей спецификации стручка.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Включить пространства Azure Dev в существующем пространстве имен с бегущими стручками

У вас может быть существующий кластер AKS и пространство имен с запущенными стручками, где требуется включить пространства Azure Dev Spaces.

Чтобы включить пространство Azure Dev в существующем пространстве `use-dev-spaces` имен `kubectl` в кластере AKS, запустите и используйте для перезапуска всех стручков в этом пространстве имен.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

После перезапуска стручков можно начать использовать существующее пространство имен с помощью Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Включить пространства Azure Dev в кластере AKS с ограниченным трафиком для кластерных узлов

Для включения пространства Azure Dev в кластере AKS, для которого ограничен трафик из кластерных узлов, необходимо разрешить следующие F-DN:

| Полное доменное имя.                                    | Порт      | Использовать      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Чтобы вытащить linux альпийских и других Azure Dev Пространства изображения |
| gcr.io | HTTP:443 | Чтобы вытащить руль / тиллер изображения|
| storage.googleapis.com | HTTP:443 | Чтобы вытащить руль / тиллер изображения|
| azds-<guid>. <location>.azds.io | HTTPS:443 | Для связи с бэкэнд-сервисами Azure Dev Spaces для вашего контроллера. Точный ФЗДН можно найти в "dataplaneFqdn" в\.%USERPROFILE% azds'settings.json|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Ошибка "Не удалось \<найти\> кластер \<кластера в подпискеId"\>

Вы можете увидеть эту ошибку, если файл kubeconfig ориентирован на другой кластер или подписку, чем вы пытаетесь использовать с помощью клиентского инструмента Azure Dev Spaces. Инструментирование клиента Azure Dev Spaces воспроизводит поведение *kubectl,* который использует [один или несколько файлов kubeconfig](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) для выбора и связи с кластером.

Чтобы устранить эту проблему:

* Используйте `az aks use-dev-spaces -g <resource group name> -n <cluster name>` для обновления текущего контекста. Эта команда также позволяет пространствам Azure Dev в кластере AKS, если он еще не включен. Кроме того, можно `kubectl config use-context <cluster name>` использовать для обновления текущего контекста.
* Используйте `az account show` для отображая текущую подписку Azure, на которая вы ориентируетесь, и проверяйте, что это правильно. Вы можете изменить подписку, `az account set`на которая вы ориентируетесь, используя.

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Ошибка использования Dev Spaces после вращения сертификатов AKS

После [вращения сертификатов в кластере AKS](../aks/certificate-rotation.md)некоторые операции, такие как `azds space list` и `azds up` не удастся. Также необходимо обновить сертификаты на контроллере Azure Dev Spaces после вращения сертификатов в кластере.

Чтобы исправить эту проблему, убедитесь, что ваш `az aks get-credentials` *kubeconfig* имеет обновленные сертификаты, используя затем запустить `azds controller refresh-credentials` команду. Пример:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
