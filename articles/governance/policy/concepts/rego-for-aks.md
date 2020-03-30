---
title: Изучите политику Azure для службы Azure Kubernetes
description: Узнайте, как Azure Policy использует агент Rego и Open Policy для управления кластерами в службе Azure Kubernetes.
ms.date: 03/27/2020
ms.topic: conceptual
ms.openlocfilehash: d77c5cf94a8239f4617e563961cbe1cc40e48fe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372657"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Общие сведения о службе "Политика Azure" для Службы Azure Kubernetes

Политика Azure интегрируется с [службой Azure Kubernetes](../../../aks/intro-kubernetes.md) (AKS) для применения в масштабах органов и гарантий в кластерах централизованным и последовательным образом.
Расширяя использование [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, _контроллера приема webhook_ для [open Policy Agent](https://www.openpolicyagent.org/) (OPA), Azure Policy позволяет управлять и сообщать о состоянии соответствия ресурсам Azure и кластерам AKS из одного места.

> [!IMPORTANT]
> Политика Azure для AKS находится в предварительном просмотре и поддерживает только встроенные определения политики. Встроенные политики относятся к категории **Kubernetes.** Эффект **EnforceRegoPolicy** и связанные с ним политики категории **Kubernetes** Service _унижаются._ Вместо этого используйте обновленный эффект [EnforceOPAConstraint.](./effects.md#enforceopaconstraint)

> [!WARNING]
> Эта функция пока доступна не во всех регионах. Для состояния развертывания см. [AKS Issues - Breaking Change for Policy Add-on](https://github.com/Azure/AKS/issues/1529)

## <a name="overview"></a>Обзор

Чтобы включить и использовать политику Azure для AKS с кластером AKS, привьйте следующие действия:

- [Включение предварительных версий функций Power BI](#opt-in-for-preview)
- [Установка дополнения политики Azure](#installation-steps)
- [Назначить определение политики для AKS](#built-in-policies)
- [Ожидание проверки](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Зарегистри:1 в предварительном просмотре

Перед установкой дополнения политики Azure или включения любой из функций службы, ваша подписка должна включить поставщика ресурсов **Microsoft.ContainerService** и поставщика ресурсов **Microsoft.PolicyInsights,** а затем получить одобрение на присоединение к предварительному просмотру. Чтобы присоединиться к предварительному просмотру, выполните следующие действия на портале Azure или с помощью Azure CLI:

- Портал Azure:

  1. Зарегистрируйте поставщиков ресурсов **Microsoft.ContainerService** и **Microsoft.PolicyInsights.** Для получения [Resource providers and types](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)шагов см.

  1. Запустите службу "Политика Azure" на портале Azure, щелкнув **Все службы**, а затем выполнив поиск и выбрав **Политика**.

     ![Поиск политики в разделе "Все службы"](../media/rego-for-aks/search-policy.png)

  1. Выберите **Предварительный просмотр** на левой стороне страницы политики Azure.

     ![Присоединиться к политике для предварительного просмотра AKS](../media/rego-for-aks/join-aks-preview.png)

  1. Выберите строку подписки, которую вы хотите добавить в предварительный просмотр.

  1. Выберите кнопку **Opt-in** в верхней части списка подписок.

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Service provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  ```

## <a name="azure-policy-add-on"></a>Дополнение политики Azure

_Дополнение политики Azure_ для Kubernetes соединяет службу политики Azure с контроллером приема Хранителя. Надстройка, которая устанавливается в пространстве имени _kube-системы,_ вводит в себя следующие функции:

- Проверка службы политики Azure для назначений в кластер.
- Развертывает политики в кластере как [шаблон ограниченности](https://github.com/open-policy-agent/gatekeeper#constraint-templates) и [ограничивают](https://github.com/open-policy-agent/gatekeeper#constraints) пользовательские ресурсы.
- Отчеты об аудите и сведениях о соответствии обратно в службу политики Azure.

### <a name="installing-the-add-on"></a>Установка надстройки

#### <a name="prerequisites"></a>Предварительные требования

Перед установкой дополнения в кластере AKS необходимо установить расширение предварительного просмотра. Этот шаг сделан с Azure CLI:

1. Если политики Gatekeeper v2 были установлены, удалите надстройку с **кнопкой «Отключить»** на кластере AKS на странице **Политики (предварительный просмотр).**

1. Вам нужна версия Azure CLI 2.0.62 или более поздняя установка и настройка. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

1. Кластер AKS должен быть версией _1.14_ или выше. Используйте следующий скрипт для проверки кластерной версии AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Установите версию _0.4.0_ расширения предварительного просмотра `aks-preview`Azure CLI для AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Если вы ранее установили расширение _aks-preview,_ установите `az extension update --name aks-preview` все обновления с помощью команды.

#### <a name="installation-steps"></a>Шаги установки

После завершения предпосылок установите дополнение политики Azure в кластер AKS, которым вы хотите управлять.

- Портал Azure

  1. Запустите сервис AKS на портале Azure, нажав **на все службы,** затем ища и выбирая **сервисы Kubernetes.**

  1. Выберите один из кластеров AKS.

  1. Выберите **политики (предварительный просмотр)** на левой стороне страницы службы Kubernetes.

     ![Политики кластера AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. На главной странице выберите кнопку **добавления Enable.**

     ![Включить политику Azure для дополнений AKS](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Если кнопка **добавления Включить** седых, подписка еще не добавлена в предварительный просмотр. [Ознакомьтесь с opt-in для предварительного просмотра](#opt-in-for-preview) необходимых шагов. Если кнопка **"Отключить"** доступна, Gatekeeper v2 по-прежнему устанавливается и должна быть удалена.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Частота валидирования и отчетности

Надстройка регистрируется в службе политики Azure для изменения заданий политики каждые 15 минут.
Во время этого цикла обновления надстройка проверяет изменения. Эти изменения вызывают создание, обновление или удаление шаблонов и ограничений ограничений.

> [!NOTE]
> Хотя у кластерного адмий-мина может быть разрешение на создание и обновление шаблонов ограничений и ресурсов ограничений, они не поддерживаются сценариями, поскольку ручные обновления будут перезаписаны.

Каждые 15 минут надстройка требует полного сканирования кластера. После сбора сведений о полном сканировании и любых оценках в реальном времени, сделанных Gatekeeper в отношении попыток изменений в кластере, надстройка сообщает результаты в службу политики Azure для включения в [детали соответствия,](../how-to/get-compliance-data.md#portal) как и любое назначение политики Azure. В цикле аудита возвращаются только результаты для активных назначений политики. Результаты аудита также можно рассматривать как [нарушения,](https://github.com/open-policy-agent/gatekeeper#audit) перечисленные в поле состояния неудавшегося ограничения.

## <a name="policy-language"></a>Язык политики

Языковая структура политики Azure для управления Kubernetes соответствует структуре существующих политик. Эффект _EnforceOPAConstraint_ используется для управления кластерами Kubernetes и использует свойства деталей, характерные для работы с [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) и Gatekeeper v3. Для получения подробной информации и примеров см. [EnforceOPAConstraint](./effects.md#enforceopaconstraint)
  
В рамках _подробной информации.constraintTemplate_ и _свойства details.constraint_ в определении политики, политика Azure передает URIs этих [пользовательских определений ресурсов](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) в дополнение. Rego — это язык, который OPA и Gatekeeper поддерживают для проверки запроса в кластер Kubernetes. Поддерживая существующий стандарт управления Kubernetes, Azure Policy позволяет повторно использовать существующие правила и сочетать их с политикой Azure для единого опыта отчетности об облаке. Для получения дополнительной информации, [см. Что такое Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="built-in-policies"></a>Встроенные политики

Чтобы найти встроенные политики для управления кластером с помощью портала Azure, выполните следующие действия:

1. Запустите службу политики Azure на портале Azure. Выберите все службы в левом стеле, а затем ищите и выберите **Политику.**

1. В левом стеле страницы политики Azure выберите **Определения**.

1. Из окна списка категорий используйте все, чтобы очистить фильтр, а затем выберите **Kubernetes.**

1. Выберите определение политики, а затем выберите кнопку **«Назначить».**

1. Установите **область** управления, подписку или группу ресурсов кластера Kubernetes, где будет применяться назначение политики.

   > [!NOTE]
   > При назначении политики Azure для определения AKS **область должна** включать ресурс кластера AKS.

1. Дайте назначению политики **имя** и **описание,** которые можно использовать для его легкого идентификации.

1. Установите [правоприменение политики](./assignment-structure.md#enforcement-mode) к одному из приведенных ниже значений.

   - **Включено** - Принудительная политика в кластере. Запросы на прием в Кuberнетс с нарушениями отклоняются.
   
   - **Инвалид** - Не применяйте политику в кластере. Запросы на прием в Kubernetes с нарушениями не отклоняют. Результаты оценки соответствия по-прежнему доступны. При развертывании новых политик для запущенных кластеров, _отключенный_ вариант полезен для тестирования политик, так как запросы на прием с нарушениями не отклонятся.

1. Нажмите кнопку **Далее**.

1. Установка **значений параметров**
   
   - Чтобы исключить области имен Kubernetes из оценки политики, укажите список областей имен в **исключениях пространства имен**параметра. Рекомендуется исключить: _кубе-система_

1. Выберите **Review + create** (Просмотреть и создать).

Кроме того, [используйте присвоить политику - Портал](../assign-policy-portal.md) quickstart, чтобы найти и назначить политику AKS. Поиск определения политики Kubernetes вместо образца 'audit vms'.

> [!IMPORTANT]
> Встроенные политики в категории **Kubernetes** предназначены только для использования с AKS. Список встроенных полисов можно узнать из [примеров Kubernetes.](../samples/built-in-policies.md#kubernetes)

## <a name="logging"></a>Ведение журнала

### <a name="azure-policy-add-on-logs"></a>Дополнительные журналы политики Azure

В качестве контроллера/контейнера Kubernetes в кластере AKS и в качестве дополнения политики Azure и Gatekeeper хранятся журналы. Эти журналы разоблачаются на странице **Insights** кластера AKS. Для получения дополнительной информации [см. Понимание производительности кластера AKS с Azure Monitor для контейнеров.](../../../azure-monitor/insights/container-insights-analyze.md)

## <a name="remove-the-add-on"></a>Удалить надстройку

Чтобы удалить дополнение политики Azure из кластера AKS, используйте либо портал Azure, либо Azure CLI:

- Портал Azure

  1. Запустите сервис AKS на портале Azure, нажав **на все службы,** затем ища и выбирая **сервисы Kubernetes.**

  1. Выберите кластер AKS, где требуется отключить надстройку с политикой Azure.

  1. Выберите **политики (предварительный просмотр)** на левой стороне страницы службы Kubernetes.

     ![Политики кластера AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. На главной странице выберите кнопку **«Отключить» для добавления.**

     ![Отменяйте политику Azure для дополнений AKS](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Диагностические данные, собранные дополнением политики Azure

Дополнение политики Azure для Kubernetes собирает ограниченные данные кластерной диагностики. Эти диагностические данные имеют жизненно важные технические данные, связанные с программным обеспечением и производительностью. Она используется для:

- Обновляйте дополнения политики Azure в актуальном состоянии
- Обестожно безопасная, надежная и работостойкая работа Йте-на-хау политики Azure
- Улучшение дополнения политики Azure - путем совокупного анализа использования надстройки

Информация, собранная в дополнении, не является персональными данными. В настоящее время собраны следующие детали:

- Версия агента по дополнению политики Azure
- Тип кластера
- Кластерный регион
- Кластерная группа ресурсов
- Идентификатор кластерных ресурсов
- Идентификатор подписки кластера
- Кластерная ОС (Пример: Linux)
- Кластерный город (Пример: Сиэтл)
- Кластерное государство или провинция (Пример: Вашингтон)
- Кластерная страна или регион (Пример: США)
- Исключения/ошибки, возникающие при добавлении политики Azure во время установки агента при оценке политики
- Количество политик Хранителя прививок, не установленных надстройкой политики Azure

## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите [примеры на примерах политики Azure](../samples/index.md).
- Просмотрите [структуру определения политики](definition-structure.md).
- Изучите [сведения о действии политик](effects.md).
- Понять, как [программно создавать политики.](../how-to/programmatically-create.md)
- Узнайте, как [получить данные о соответствии.](../how-to/get-compliance-data.md)
- Узнайте, как [исправления несовместимых ресурсов.](../how-to/remediate-resources.md)
- Просмотрите, что представляет собой группа управления с [организацией ресурсов с группами управления Azure.](../../management-groups/overview.md)
