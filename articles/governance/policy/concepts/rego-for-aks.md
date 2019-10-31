---
title: Сведения о политике Azure для службы Kubernetes Azure
description: Узнайте, как политика Azure использует Рего и Open Policy Agent для управления кластерами в службе Kubernetes Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 6a3d1fb347819015887ffc4fd8089bbc1f3a70de
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176317"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Общие сведения о политике Azure для службы Kubernetes Azure

Политика Azure интегрируется со [службой Azure Kubernetes](../../../aks/intro-kubernetes.md) (AKS), чтобы обеспечить централизованное и согласованное применение ограничений и мер безопасности в кластерах.
Благодаря расширению использования [привратника](https://github.com/open-policy-agent/gatekeeper), _веб-перехватчика контроллера допуска_ для [Open Policy Agent](https://www.openpolicyagent.org/) (непрозра), политика Azure позволяет управлять состоянием соответствия ресурсов Azure и кластеров AKS из одного места и сообщать о нем.

> [!NOTE]
> Политика Azure для AKS ограничена предварительной версией и поддерживает только встроенные определения политик.

## <a name="overview"></a>Краткое описание

Чтобы включить и использовать политику Azure для AKS с кластером AKS, выполните следующие действия.

- [Согласие на предварительную версию функций](#opt-in-for-preview)
- [Установка надстройки политики Azure](#installation-steps)
- [Назначение определения политики для AKS](#built-in-policies)
- [Ожидание проверки](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Согласие на предварительную версию

Прежде чем устанавливать надстройку политики Azure или включить какие-либо функции службы, ваша подписка должна включить поставщик ресурсов **Microsoft. ContainerService** и поставщик ресурсов **Microsoft. полициинсигхтс** , а затем утвердить его. Присоединяйтесь к предварительной версии. Чтобы присоединиться к предварительной версии, выполните следующие действия в портал Azure или с помощью Azure CLI.

- На портале Azure.

  1. Зарегистрируйте поставщики ресурсов **Microsoft. ContainerService** и **Microsoft. полициинсигхтс** . Инструкции см. в разделе [поставщики и типы ресурсов](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

  1. Запустите службу "Политика Azure" на портале Azure, щелкнув **Все службы**, а затем выполнив поиск и выбрав **Политика**.

     ![Поиск политики в разделе "Все службы"](../media/rego-for-aks/search-policy.png)

  1. В левой части страницы политики Azure выберите **Предварительный просмотр присоединение** .

     ![Присоединение политики к предварительной версии AKS](../media/rego-for-aks/join-aks-preview.png)

  1. Выберите строку подписки, которую нужно добавить в предварительную версию.

  1. Нажмите кнопку **согласие** в верхней части списка подписок.

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.PolicyInsights/AKS-DataPlaneAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.PolicyInsights
  
  ```

## <a name="azure-policy-add-on"></a>Надстройка политики Azure

_Надстройка политики Azure_ для Kubernetes подключает службу политики Azure к контроллеру допуска привратника. Надстройка, устанавливаемая в пространство имен _Azure-Policy_ , вводит следующие функции:

- Проверяет наличие политики Azure для назначений в кластере AKS
- Загружает и кэширует сведения о политике, включая определение политики _Рего_ , как **конфигмапс**
- Выполнение проверки соответствия полной проверки на кластере AKS
- Сообщает сведения об аудите и соответствии политике Azure

### <a name="installing-the-add-on"></a>Установка надстройки

#### <a name="prerequisites"></a>Технические условия

Перед установкой надстройки в кластере AKS необходимо установить расширение предварительной версии. Этот шаг выполняется с Azure CLI:

1. Требуется Azure CLI версии 2.0.62 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

1. Кластер AKS должен иметь версию _1,10_ или более позднюю. Используйте следующий скрипт для проверки версии кластера AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Установите версию _0.4.0_ предварительной версии расширения Azure CLI для AKS, `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Если вы ранее установили расширение _AKS-Preview_ , установите все обновления с помощью команды `az extension update --name aks-preview`.

#### <a name="installation-steps"></a>Шаги установки

После завершения предварительных требований установите надстройку политики Azure в кластере AKS, которым вы хотите управлять.

- портала Azure

  1. Запустите службу AKS в портал Azure, щелкнув **все службы**, а затем выполнив поиск и выбрав **Kubernetes Services**.

  1. Выберите один из кластеров AKS.

  1. В левой части страницы службы Kubernetes выберите **политики (Предварительная версия)** .

     ![Политики из кластера AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. На главной странице нажмите кнопку **включить надстройку** .

     ![Включение надстройки "политика Azure для AKS"](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Если кнопка **включить надстройка** неактивна, подписка еще не добавлена в предварительную версию. Необходимые шаги см. [в разделе согласие на предварительную версию](#opt-in-for-preview) .

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Периодичность проверки и отчетности

Надстройка проверяет в политике Azure изменения назначений политик каждые 5 минут. Во время этого цикла обновления надстройка удаляет все _конфигмапс_ в пространстве имен _Azure-Policy_ , а затем повторно создает _конфигмапс_ для использования привратника.

> [!NOTE]
> Хотя _Администратор кластера_ может иметь разрешение на пространство имен _политики Azure_ , он не рекомендуется или не поддерживается для внесения изменений в пространство имен. Все внесенные вручную изменения теряются во время цикла обновления.

Каждые 5 минут надстройка вызывает полную проверку кластера. После сбора подробных сведений о полной проверке и любых оценках в реальном времени с помощью привратника попыток внести изменения в кластер, эта надстройка выводит результаты обратно в политику Azure для включения в [сведения о соответствии](../how-to/get-compliance-data.md) , например любое назначение политики Azure. Во время цикла аудита возвращаются только результаты активных назначений политик.

## <a name="policy-language"></a>Язык политики

Языковая структура политики Azure для управления AKS соответствует существующим политикам. Действие _енфорцерегополици_ используется для управления кластерами AKS и принимает свойства _сведений_ , относящиеся к работе с непрозра и привратником. Дополнительные сведения и примеры см. в разделе [енфорцерегополици](effects.md#enforceregopolicy) Effect.

Как часть свойства _Details. Policy_ в определении политики, политика Azure передает универсальный код ресурса (URI) политики Рего в надстройку. Рего — это язык, который поддержка НЕПРОЗРА и привратника для проверки или изменения запроса к кластеру Kubernetes. Благодаря поддержке существующего стандарта для управления Kubernetes, политика Azure позволяет повторно использовать существующие правила и связывать их с политикой Azure для единого создания отчетов о соответствии с облаком. Дополнительные сведения см. в разделе [что такое Рего?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="built-in-policies"></a>Встроенные политики

Чтобы найти встроенные политики для управления AKS с помощью портал Azure, выполните следующие действия.

1. Запустите службу политики Azure в портал Azure. Выберите **все службы** на левой панели, а затем найдите и выберите **Политика**.

1. В левой области страницы политика Azure выберите **определения**.

1. В раскрывающемся списке Категория **выберите команду выбрать все** , чтобы очистить фильтр, а затем выберите **Kubernetes Service**.

1. Выберите определение политики, а затем нажмите кнопку **назначить** .

> [!NOTE]
> При назначении политики Azure для определения AKS **область** должна включать ресурс кластера AKS.

Кроме того, для поиска и назначения политики AKS используйте краткое руководство по [назначению политики](../assign-policy-portal.md) с помощью портала. Выполните поиск определения политики Kubernetes вместо примера "Audit VMS".

## <a name="logging"></a>Ведение журнала

### <a name="azure-policy-add-on-logs"></a>Журналы надстроек для политики Azure

Как контроллер или контейнер Kubernetes, надстройка политики Azure хранит журналы в кластере AKS. Журналы отображаются на странице **Insights** кластера AKS. Дополнительные сведения см. [в статье производительность кластера AKS с Azure Monitor для контейнеров](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>Журналы привратника

Чтобы включить журналы привратника для новых запросов ресурсов, выполните действия, описанные в разделе [Включение и проверка журналов главного узла Kubernetes в AKS](../../../aks/view-master-logs.md).
Ниже приведен пример запроса для просмотра отклоненных событий для новых запросов ресурсов.

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Чтобы просмотреть журналы из контейнеров привратника, выполните действия, описанные в разделе [Включение и просмотр журналов главного узла Kubernetes в AKS](../../../aks/view-master-logs.md) и проверьте параметр _KUBE-аписервер_ в области **параметры диагностики** .

## <a name="remove-the-add-on"></a>Удаление надстройки

Чтобы удалить надстройку политики Azure из кластера AKS, используйте либо портал Azure, либо Azure CLI.

- портала Azure

  1. Запустите службу AKS в портал Azure, щелкнув **все службы**, а затем выполнив поиск и выбрав **Kubernetes Services**.

  1. Выберите кластер AKS, в котором необходимо отключить надстройку политики Azure.

  1. В левой части страницы службы Kubernetes выберите **политики (Предварительная версия)** .

     ![Политики из кластера AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. На главной странице нажмите кнопку **отключить надстройку** .

     ![Отключите политику Azure для надстройки AKS](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите примеры в [примерах политики Azure](../samples/index.md).
- См. дополнительные сведения о [структуре определения Политики Azure](definition-structure.md).
- Изучите [сведения о действии политик](effects.md).
- Узнайте, как [программно создавать политики](../how-to/programmatically-create.md).
- Узнайте, как [получить данные о соответствии](../how-to/getting-compliance-data.md).
- Узнайте, как [исправлять несоответствующие ресурсы](../how-to/remediate-resources.md).
- Дополнительные сведения о группе управления см. в статье [Упорядочивание ресурсов с помощью групп управления Azure](../../management-groups/overview.md).
