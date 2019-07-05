---
title: Узнайте, как аудит содержимого виртуальной машины
description: Узнайте, как "Политика Azure" использует Rego и откройте агент политики для управления кластерами в службе Azure Kubernetes.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fdb392533e28df1d50e90c842d0117385afb254b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453909"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Общие сведения о "Политика Azure" для службы Azure Kubernetes

Политика Azure интегрируется с [службе Azure Kubernetes](../../../aks/intro-kubernetes.md) (AKS) для применения в масштабе принудительные применения и меры безопасности в кластерах образом централизованное, согласованный.
Путем расширения использование [привратника](https://github.com/open-policy-agent/gatekeeper), _веб-перехватчика контроллера допуском_ для [откройте агент политики](https://www.openpolicyagent.org/) (OPA), "Политика Azure" дает возможность управлять и отчеты о соответствии состояние ресурсов Azure и AKS кластеры из одного места.

> [!NOTE]
> Политика Azure для AKS находится в ограниченной предварительной версии и поддерживает только определения встроенных политик.

## <a name="overview"></a>Обзор

Чтобы включить и использовать политику Azure для AKS с кластером AKS, выполните следующие действия:

- [Подписаться на этапе предварительной версии](#opt-in-for-preview)
- [Установить надстройку "Политика Azure"](#installation-steps)
- [Назначение определения политики для AKS](#built-in-policies)
- [Подождите, проверка](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Подписаться на предварительной версии

Перед установкой надстройки политики Azure или включение любой функции службы, ваша подписка должна позволять **Microsoft.ContainerService** поставщика ресурсов и **Microsoft.PolicyInsights**поставщика ресурсов, а затем утверждаться для получения предварительной версии. Для получения предварительной версии, выполните следующие действия, либо на портале Azure или с помощью Azure CLI.

- Портал Azure:

  1. Зарегистрировать **Microsoft.ContainerService** и **Microsoft.PolicyInsights** поставщиков ресурсов. Действия, см. в разделе [поставщики и типы ресурсов](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

  1. Запустите службу "Политика Azure" на портале Azure, щелкнув **Все службы**, а затем выполнив поиск и выбрав **Политика**.

     ![Поиск политики в разделе "Все службы"](../media/rego-for-aks/search-policy.png)

  1. Выберите **присоединиться к предварительной версии** в левой части страницы "Политика Azure".

     ![Присоединение политики для предварительной версии AKS](../media/rego-for-aks/join-aks-preview.png)

  1. Выберите строку удаляемого подписку, которую можно добавить в предварительной версии.

  1. Выберите **согласием** кнопку в верхней части списка подписок.

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove

  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  ```

## <a name="azure-policy-add-on"></a>Надстройка "Политика Azure"

_Надстройка политики Azure_ для Kubernetes службы "Политика Azure" подключается к контроллеру допуском привратника. Надстройка, устанавливаемая в _политика azure_ пространства имен, вводит в действие следующие функции:

- Проверки с помощью политики Azure для назначения в кластере AKS
- Загружает и кэширует сведения о политике, включая _rego_ Определение политики, как **configmaps**
- Запускает проверку соответствия полную проверку в кластере AKS
- Отчеты аудита и сведения о соответствии обратно в "Политика Azure"

### <a name="installing-the-add-on"></a>Установка надстройки

#### <a name="prerequisites"></a>Технические условия

Перед установкой надстройки в кластере AKS, необходимо установить расширения предварительной версии. Этот шаг выполняется с помощью Azure CLI:

1. Требуется Azure CLI версии 2.0.62 или более поздней версии установлен и настроен. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

1. Кластер AKS должны иметь версию _1.10_ или более поздней версии. Для проверки вашей версии кластера AKS, используйте следующий сценарий:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Установите версию _0.4.0_ интерфейс Azure CLI предварительной версии расширения для AKS, `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Если вы ранее установили _предварительной версии aks_ расширения, установка обновлений с помощью `az extension update --name aks-preview` команды.

#### <a name="installation-steps"></a>Шаги установки

После выполнения предварительных условий, установите надстройку "Политика Azure" в кластере AKS, которым вы хотите управлять.

- Портал Azure

  1. Запустите службы AKS на портале Azure, щелкнув **все службы**, поиск, выберите **служб Kubernetes**.

  1. Выберите один из ваших кластерах AKS.

  1. Выберите **политик (Предварительная версия)** в левой части страницы службы Kubernetes.

     ![Политики из кластера AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. На главной странице выберите **включить надстройку** кнопки.

     ![Включить политику Azure для AKS надстройки](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Если **включить надстройку** кнопка будет недоступна, подписка не был добавлен к предварительной версии. См. в разделе [согласием для предварительной версии](#opt-in-for-preview) необходимые действия.

- Инфраструктура CLI Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Проверка и частота отчетов

Надстройка проверяет систему с помощью "Политика Azure" для изменения в назначения политик каждые 5 минут. В ходе этого цикла обновления удаляет надстройку все _configmaps_ в _политика azure_ пространства имен, затем повторно создается _configmaps_ для использования привратника.

> [!NOTE]
> Хотя _кластера администратора_ может иметь разрешения на _политика azure_ пространства имен, его не рекомендуется или вносить изменения в пространство имен. Изменения, вносимые будут потеряны во время цикла обновления.

Каждые 5 минут, надстройка вызывает для полной проверки кластера. После сбора особенностей полная проверка и любые в режиме реального времени ознакомительные версии по привратника попытки изменений в кластер, надстройка сообщает о результатах обратно в политику Azure для включения в [сведения о соответствии](../how-to/get-compliance-data.md) любую политику Azure, такие как Назначение. Только для назначений политик active результаты во время цикла аудита.

## <a name="policy-language"></a>Язык политики

Структура языка "Политика Azure" для управления AKS следует, что существующих политик. Эффект _EnforceRegoPolicy_ используется для управления кластерами AKS и принимает _сведения_ свойства, относящиеся к работе с OPA и привратника. Дополнительные сведения и примеры, см. в разделе [EnforceRegoPolicy](effects.md#enforceregopolicy) эффект.

Как часть _details.policy_ свойства в определении политики "Политика Azure" передает URI rego политики в надстройку. Rego — это язык, OPA и Привратник поддержки, чтобы проверить или изменить запрос к кластеру Kubernetes. Благодаря поддержке существующим стандартом для управления Kubernetes, "Политика Azure" дает возможность повторно использовать существующие правила и свяжите их с помощью политики Azure для соответствия единой облака, отчеты. Дополнительные сведения см. в разделе [Какова Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Встроенные политики

Чтобы найти встроенные политики для управления AKS с помощью портала Azure, выполните следующие действия.

1. Запустите службу "Политика Azure" на портале Azure. Выберите **все службы** в левой области и затем найдите и выберите **политики**.

1. В левой части страницы "Политика Azure", выберите **определения**.

1. Из раскрывающегося списка поле категории можно использовать **выбрать все** Очистить фильтр, а затем нажмите кнопку **службы Kubernetes**.

1. Выберите Определение политики, а затем **назначить** кнопки.

> [!NOTE]
> При назначении политики Azure для определения AKS **область** должен включать ресурсы кластера AKS.

Кроме того, с помощью [назначение политики — портал](../assign-policy-portal.md) быстрого запуска, чтобы найти и назначьте политику AKS. Найдите определение политики Kubernetes вместо образец «audit vms».

## <a name="logging"></a>Ведение журналов

### <a name="azure-policy-add-on-logs"></a>Журналы Azure надстройки политики

В качестве контроллера/контейнера Kubernetes надстройку "Политика Azure" сохраняет журналы в кластере AKS. Журналы, представлены в **Insights** страницы кластера AKS. Дополнительные сведения см. в разделе [производительности с помощью Azure Monitor для контейнеров кластера AKS понять](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>Журналы привратника

Чтобы включить журналы привратника для новых запросов ресурсов, выполните действия, описанные в [включить и просматривать журналы главного узла в AKS Kubernetes](../../../aks/view-master-logs.md).
Ниже приведен пример запроса, чтобы просмотреть отклоненные события на новые запросы ресурсов.

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Для просмотра журналов из контейнеров привратника, выполните действия, описанные в [включить и просматривать журналы главного узла в AKS Kubernetes](../../../aks/view-master-logs.md) и проверьте _kube-apiserver_ в диалоговом окне **параметров диагностики** области.

## <a name="remove-the-add-on"></a>Удалить надстройку

Чтобы удалить надстройку "Политика Azure" из кластера AKS, используйте портал Azure или Azure CLI:

- Портал Azure

  1. Запустите службы AKS на портале Azure, щелкнув **все службы**, поиск, выберите **служб Kubernetes**.

  1. Выберите свой кластер AKS, где вы хотите отключить надстройку "Политика Azure".

  1. Выберите **политик (Предварительная версия)** в левой части страницы службы Kubernetes.

     ![Политики из кластера AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. На главной странице выберите **отключение надстройки** кнопки.

     ![Отключить политику Azure для AKS надстройки](../media/rego-for-aks/disable-policy-add-on.png)

- Инфраструктура CLI Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите примеры доступны на [примеры политик Azure](../samples/index.md).
- См. дополнительные сведения о [структуре определения Политики Azure](definition-structure.md).
- Изучите [сведения о действии политик](effects.md).
- Понять, как [программное создание политик](../how-to/programmatically-create.md).
- Узнайте, как [получить данные о соответствии](../how-to/getting-compliance-data.md).
- Узнайте, как [исправлять несоответствующие ресурсы](../how-to/remediate-resources.md).
- Дополнительные сведения о группе управления см. в статье [Упорядочивание ресурсов с помощью групп управления Azure](../../management-groups/index.md).