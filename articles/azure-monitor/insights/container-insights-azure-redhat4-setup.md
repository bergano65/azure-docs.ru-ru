---
title: Настройка Azure Red Hat OpenShift v4. x с Azure Monitor для контейнеров | Документация Майкрософт
description: В этой статье описывается настройка мониторинга кластера Kubernetes с Azure Monitor, размещенных в Azure Red Hat OpenShift версии 4 и выше.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 4b827524845874dabaabe535163d99c408f77a60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196300"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Настройка Azure Red Hat OpenShift v4. x с Azure Monitor для контейнеров

Azure Monitor для контейнеров предоставляет широкие возможности мониторинга для кластеров подсистемы Azure Kubernetes Service (AKS) и AKS. В этой статье описывается, как включить мониторинг кластеров Kubernetes, размещенных в [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) версии 4. x, для обеспечения аналогичного процесса мониторинга.

>[!NOTE]
>Поддержка Azure Red Hat OpenShift в настоящее время является функцией в общедоступной предварительной версии.
>

Azure Monitor для контейнеров можно включить для одного или нескольких существующих развертываний Azure Red Hat OpenShift v4. x, используя следующие поддерживаемые методы:

- Для существующего кластера с помощью предоставленного скрипта Bash и запуска в [Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Поддерживаемые и неподдерживаемые функции

Azure Monitor для контейнеров поддерживает мониторинг Azure Red Hat OpenShift v4. x, как описано в [обзорной](container-insights-overview.md) статье, за исключением следующих возможностей:

- Данные в режиме реального времени (Предварительная версия)
- [Получение метрик](container-insights-update-metrics.md) из узлов кластера и модулей Pod и их сохранение в базе данных метрик Azure Monitor

## <a name="prerequisites"></a>Предварительные требования

- Azure CLI версии 2.0.72 или выше

- [Helm 3](https://helm.sh/docs/intro/install/) Средство CLI

- [Bash версии 4](https://www.gnu.org/software/bash/)

- Программа командной строки [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Чтобы включить функции и получить доступ к ним в Azure Monitor для контейнеров, как минимум необходимо быть участником роли *участника* Azure в подписке Azure и членом роли [*участника log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) log Analytics рабочей области, настроенной с Azure Monitor для контейнеров.

- Для просмотра данных мониторинга вы являетесь членом разрешения роли [*log Analytics читатель*](../platform/manage-access.md#manage-access-using-azure-permissions) с рабочей областью log Analytics, настроенной с Azure Monitor для контейнеров.

## <a name="enable-for-an-existing-cluster"></a>Включить для существующего кластера

Выполните следующие действия, чтобы включить мониторинг кластера Azure Red Hat OpenShift версии 4 и более поздних версий, развернутого в Azure, с помощью предоставленного скрипта bash.

1. Вход в Azure

    ```azurecli
    az login
    ```

2. Скачайте и сохраните скрипт в локальной папке, которая настраивает кластер с помощью надстройки мониторинга, используя следующие команды:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

3. Чтобы найти **KUBE-context** кластера, после успешного `oc login` завершения в кластере выполните команду `kubectl config current-context` и скопируйте значение.

### <a name="integrate-with-an-existing-workspace"></a>Интеграция с существующей рабочей областью

Следующий шаг включает мониторинг кластера с помощью скрипта bash, скачанного ранее. Чтобы выполнить интеграцию с существующей рабочей областью Log Analytics, выполните следующие действия, чтобы сначала указать полный идентификатор ресурса Log Analytics рабочей области, необходимой для `workspaceResourceId` параметра, а затем выполнить команду, чтобы включить надстройку мониторинга для указанной рабочей области. Если у вас нет рабочей области для указания, можно перейти к шагу 5 и позволить сценарию создать новую рабочую область.

1. Перечислите все подписки, к которым у вас есть доступ, с помощью следующей команды:

    ```azurecli
    az account list --all -o table
    ```

    Результат должен выглядеть так:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Скопируйте значение для **SubscriptionId**.

2. Перейдите к подписке, в которой размещена рабочая область Log Analytics, с помощью следующей команды:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. В следующем примере отображается список рабочих областей в подписках в формате JSON по умолчанию.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    В выходных данных найдите имя рабочей области, а затем скопируйте полный идентификатор ресурса этой Log Analytics рабочей области под **идентификатором**поля.

4. Выполните следующую команду, чтобы включить мониторинг, заменив значение `workspaceResourceId` параметра: 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <LogAnayticsWorkspaceResourceId>`

    Пример.

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4  /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

После включения мониторинга может пройти около 15 минут, прежде чем вы сможете просмотреть метрики работоспособности кластера.

### <a name="integrate-with-default-workspace"></a>Интеграция с рабочей областью по умолчанию

Следующий шаг включает мониторинг кластера Azure Red Hat OpenShift v4. x с помощью скачанного скрипта bash. В этом примере вы не обязаны предварительно создавать или указывать имеющуюся рабочую область. Эта команда упрощает процесс, создавая рабочую область по умолчанию в группе ресурсов по умолчанию подписки на кластер, если она еще не существует в регионе. Имя созданной рабочей области по умолчанию поддерживает формат *DefaultWorkspace-\<GUID>-\<Region>*.  

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

    For example:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

После включения мониторинга может пройти около 15 минут, прежде чем вы сможете просмотреть метрики работоспособности кластера.

### <a name="from-the-azure-portal"></a>на портале Azure;

В представлении с несколькими кластерами в Azure Monitor для контейнеров выделяются кластеры Azure Red Hat OpenShift, для которых не включена поддержка мониторинга на вкладке **Неотслеживаемые кластеры** . Параметр **включить** рядом с кластером не инициирует подключение мониторинга с портала. Вы будете перенаправлены к этой статье, чтобы включить наблюдение вручную, выполнив действия, описанные ранее в этой статье.

1. Войдите на [портал Azure](https://portal.azure.com).

2. В меню портал Azure или на домашней странице выберите **Azure Monitor**. В разделе **Аналитика** выберите **Контейнеры**.

3. На странице **Контейнеры** выберите **Non-monitored clusters** (Неотслеживаемые кластеры).

4. В списке неотслеживаемых кластеров найдите кластер в списке и нажмите кнопку **включить**. Результаты в списке можно найти, выполнив поиск значения **АТО** в столбце **тип кластера**. После нажатия кнопки **включить**вы будете перенаправлены к этой статье.

## <a name="next-steps"></a>Дальнейшие действия

- С включенным наблюдением для получения сведений о работоспособности и использовании ресурсов для кластера RedHat OpenShift версии 4. x и рабочих нагрузок, выполняемых на них, Узнайте, [как использовать](container-insights-analyze.md) Azure Monitor для контейнеров.

- По умолчанию контейнерный агент собирает журналы контейнеров stdout/stderr всех контейнеров, работающих во всех пространствах имен, за исключением KUBE-System. Чтобы настроить сбор журналов контейнеров для конкретного пространства имен или пространств имен, ознакомьтесь с разрешениями [Конфигурация агента аналитики контейнеров](container-insights-agent-config.md) , чтобы настроить необходимые параметры сбора данных в файле конфигурации ConfigMap.

- Чтобы отбракировать и проанализировать метрики Prometheus из кластера, см. раздел [Настройка брака/отхода метрик Prometheus](container-insights-prometheus-integration.md)

- Сведения о том, как отключить мониторинг кластера с помощью Azure Monitor для контейнеров, см. в статье [как отключить мониторинг кластера Azure Red Hat OpenShift](container-insights-optout-openshift.md).
