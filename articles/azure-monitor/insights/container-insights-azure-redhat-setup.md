---
title: Напередите кластеры Azure Red Hat OpenShift с помощью Azure Monitor для контейнеров Документы Майкрософт
description: В этой статье описывается, как настроить мониторинг кластера Kubernetes с помощью Azure Monitor, размещенного на Azure Red Hat OpenShift.
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: c2fd3568be2c51296bb1377e91031ebfb7ca6ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275520"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Напередить кластеры Azure Red Hat OpenShift с помощью Azure Monitor для контейнеров

Azure Monitor для контейнеров предоставляет богатый опыт мониторинга для кластеров Azure Kubernetes (AKS) и AKS Engine. В этой статье описывается, как обеспечить мониторинг кластеров Kubernetes, размещенных на [Azure Red Hat OpenShift,](../../openshift/intro-openshift.md) для достижения аналогичного опыта мониторинга.

>[!NOTE]
>Поддержка Azure Red Hat OpenShift — функция, представленная в открытом доступе.
>

Azure Monitor для контейнеров может быть включен для новых или нескольких существующих развертываний Azure Red Hat OpenShift с использованием следующих поддерживаемых методов:

- Для существующего кластера с портала Azure или с помощью шаблона Azure Resource Manager.
- Для нового кластера с использованием шаблона управления ресурсами Azure или при создании нового кластера с использованием [Azure CLI.](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create)

## <a name="supported-and-unsupported-features"></a>Поддерживаемые и неподдерживаемые функции

Azure Monitor для контейнеров поддерживает мониторинг Azure Red Hat OpenShift, как описано в статье [Обзор,](container-insights-overview.md) за исключением следующих функций:

- Данные в реальном времени (предварительный просмотр)
- [Сбор метрик](container-insights-update-metrics.md) из кластерных узлов и стручков и их хранение в базе данных метрик Azure Monitor

## <a name="prerequisites"></a>Предварительные требования

- Для включения и доступа к функциям в Azure Monitor для контейнеров необходимо как минимум быть участником роли *вкладчика* Azure в подписке Azure и членом роли участника анализа [*журналов*](../platform/manage-access.md#manage-access-using-azure-permissions) рабочего пространства анализа журналов, настроенной с Azure Monitor для контейнеров.

- Для просмотра данных мониторинга вы являетесь участником разрешения на ролевую роль [*чтения журнала Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) с рабочим пространством Log Analytics, настроенным с Azure Monitor для контейнеров.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Включить новый кластер с помощью шаблона управления ресурсами Azure

Выполните следующие шаги для развертывания кластера Azure Red Hat OpenShift с включенным мониторингом. Прежде чем приступить к работе, просмотрите учебник [Создайте кластер Azure Red Hat OpenShift,](../../openshift/tutorial-create-cluster.md#prerequisites) чтобы понять зависимости, которые необходимо настроить для правильной настройки среды.

В этом методе используются два шаблона JSON. Один шаблон определяет конфигурацию для развертывания кластера с включенным мониторингом, а другой содержит значения параметров, которые вы настраиваете для указания следующих:

- Идентификатор кластера Azure Red Hat OpenShift OpenShift.

- Ресурсная группа, в ней развернута группа ресурсов.

- [Идентификатор арендатора Active Directory Azure](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) отметился после выполнения шагов для создания уже созданного.

- [Идентификатор приложения клиента Azure Active Directory,](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) отмеченный после выполнения шагов для создания уже созданного или созданного.

- [Секрет клиента Azure Active Directory,](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) отмеченный после выполнения шагов по созданию уже созданного.

- [Группа безопасности Azure AD](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) отметила после выполнения шагов по созданию уже созданного.

- Идентификатор ресурсов существующего рабочего пространства анализа журналов.

- Количество узлов мастер-технологий для создания в кластере.

- Количество вычислительных узлов в профиле пула агентов.

- Количество узлов инфраструктуры в профиле пула агентов.

Если вы не знакомы с концепцией развертывания ресурсов с помощью шаблона, ознакомьтесь со статьями:

- [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Вы должны запустить версию Azure CLI 2.0.65 или позже. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

Рабочее пространство для анализа журналов должно быть создано, прежде чем включить мониторинг с помощью Azure PowerShell или CLI. Для создания рабочей области можно использовать [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) или [портал Azure](../../azure-monitor/learn/quick-create-workspace.md).

1. Скачать и сохранить в локальной папке, шаблоне и файле параметра Azure Resource Manager, чтобы создать кластер с дополнением к мониторингу с использованием следующих команд:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Вход в Azure

    ```azurecli
    az login    
    ```

    Если у вас есть доступ к нескольким подпискам, выполните команду `az account set -s {subscription ID}`, заменив `{subscription ID}` необходимой подпиской.

3. Создайте группу ресурсов для кластера, если она еще не у вас есть. Список регионов Azure, поддерживающих OpenShift [Supported Regions](../../openshift/supported-resources.md#azure-regions)на Azure, см.

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Отобразите файл параметра JSON **newClusterWithMonitoringParam.json** и обновите следующие значения:

    - *Расположение*
    - *кластерНоеимя*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *вычислитьNodeCount*
    - *infraNodeCount*

5. Следующий шаг развертывает кластер с включенным мониторингом с помощью Azure CLI.

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    Результат выглядит следующим образом:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Включить существующий кластер

Выполните следующие действия, чтобы обеспечить мониторинг кластера Azure Red Hat OpenShift, развернутого в Azure. Вы можете сделать это с портала Azure или с помощью предоставленных шаблонов.

### <a name="from-the-azure-portal"></a>на портале Azure;

1. Войдите на [портал Azure](https://portal.azure.com).

2. В меню портала Azure или на главной странице выберите **Azure Monitor.** В разделе **Аналитика** выберите **Контейнеры**.

3. На странице **Контейнеры** выберите **Non-monitored clusters** (Неотслеживаемые кластеры).

4. Из списка неконтролируемых кластеров найдите кластер в списке и нажмите **Enable.** Вы можете определить результаты в списке, ища значение **ARO** под столбецом **CLUSTER TYPE.**

5. Если рабочая область Log Analytics расположена в той же подписке, что и кластер, выберите ее из раскрывающегося списка на странице **Onboarding to Azure Monitor for containers** (Подключение к Azure Monitor для контейнеров).  
    Список предварительно выбирает рабочее пространство по умолчанию и местоположение, развернутое кластером в подписке.

    ![Включить мониторинг для неконтролируемых кластеров](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Если вы хотите создать рабочую область Log Analytics для хранения данных мониторинга из кластера, выполните инструкции, описанные в статье [Создание рабочей области Log Analytics на портале Azure](../../azure-monitor/learn/quick-create-workspace.md). Обязательно создайте рабочее пространство в той же подписке, в которую развернут кластер RedHat OpenShift.

После включения мониторинга может пройти около 15 минут, прежде чем вы сможете просмотреть метрики работоспособности кластера.

### <a name="enable-using-an-azure-resource-manager-template"></a>Включить шаблон управления ресурсами Azure

В этом методе используются два шаблона JSON. Один шаблон задает конфигурацию для включения мониторинга, а другой содержит значения параметров, которые можно настроить, чтобы указать следующее:

- Идентификатор кластера Azure RedHat OpenShift OpenShift.

- Ресурсная группа, в ней развернута группа ресурсов.

- Рабочая область Log Analytics.

Если вы не знакомы с концепцией развертывания ресурсов с помощью шаблона, ознакомьтесь со статьями:

- [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Вы должны запустить версию Azure CLI 2.0.65 или позже. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

Рабочее пространство для анализа журналов должно быть создано, прежде чем включить мониторинг с помощью Azure PowerShell или CLI. Для создания рабочей области можно использовать [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) или [портал Azure](../../azure-monitor/learn/quick-create-workspace.md).

1. Загрузите файл шаблона и параметра для обновления кластера с помощью следующих команд:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Вход в Azure

    ```azurecli
    az login    
    ```

    Если у вас есть доступ к нескольким подпискам, выполните команду `az account set -s {subscription ID}`, заменив `{subscription ID}` необходимой подпиской.

3. Укажите подписку кластера Azure RedHat OpenShift.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Выполнить следующую команду для определения местоположения кластера и идентификатора ресурсов:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Изналивайте файл параметра **JSON, существующийClusterParam.json** и обновляйте значения *araResourceId* и *araResoruceLocation.* Значение для **workspaceResourceId** — это полный идентификатор ресурса рабочей области Log Analytics, который включает имя рабочей области.

6. Чтобы развернуть с Azure CLI, запустите следующие команды:

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    Результат выглядит следующим образом:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Дальнейшие действия

- Благодаря мониторингу, позволяющим собирать работоспособность и использование ресурсов кластера RedHat OpenShift и рабочих нагрузок, работающих на них, [научитесь использовать](container-insights-analyze.md) Azure Monitor для контейнеров.

- Чтобы узнать, как прекратить мониторинг кластера с [How to Stop Monitoring Your Azure Red Hat OpenShift cluster](container-insights-optout-openshift.md)помощью Azure Monitor для контейнеров, см.
