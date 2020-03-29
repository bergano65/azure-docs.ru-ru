---
title: Изучите политику Azure для движка AKS
description: Узнайте, как Политика Azure использует CustomResourceDefinitions и Open Policy Agent от Gatekeeper v3 для управления кластерами с помощью AKS Engine.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436425"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Понимание политики Azure для двигателя AKS

Azure Policy интегрируется с [системой AKS Engine,](https://github.com/Azure/aks-engine/blob/master/docs/README.md)которая обеспечивает удобное оборудование для быстрого загрузки самоуправляемого кластера Kubernetes на Azure. Эта интеграция обеспечивает применение и гарантии в масштабах для самоуправляемых кластеров AKS Engine централизованно и последовательно. Расширяя использование [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3 (бета), _контроллера приема для_ Kubernetes, Azure Policy позволяет управлять и сообщать о состоянии соответствия ресурсам Azure и самоуправляемым кластерам AKS Engine из одного места.

> [!NOTE]
> Политика Azure для AKS Engine находится в публичном предварительном просмотре и не имеет SLA. Gatekeeper v3 находится в бета-версии и поддерживается сообществом с открытым исходным кодом. Служба поддерживает только встроенные определения политики и единый кластер AKS Engine для каждой группы ресурсов, настроенный с принципом обслуживания.

> [!IMPORTANT]
> Чтобы получить поддержку политики Azure для AKS Engine, AKS Engine или Gatekeeper v3, создайте [новую проблему](https://github.com/Azure/aks-engine/issues/new/choose) в репозитории AKS Engine GitHub.

## <a name="overview"></a>Обзор

Чтобы включить и использовать политику Azure для AKS Engine с вашим самоуправляемым кластером Kubernetes в Azure, привите следующие действия:

- [Предварительные требования](#prerequisites)
- [Установка дополнения политики Azure](#installing-the-add-on)
- [Назначить определение политики для ДВИГАТЕЛЯ AKS](#built-in-policies)
- [Ожидание проверки](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Предварительные требования

Перед установкой дополнения политики Azure или включения каких-либо функций службы подписка должна включить поставщика ресурсов **Microsoft.PolicyInsights** и создать ролевую задачу для основного кластерного сервиса. 

1. Чтобы включить поставщика ресурсов, выполните действия [поставщиков ресурсов и типы](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) или запустите команду Azure CLI или команду Azure PowerShell:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell
   
     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
   
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Создание назначения ролей для основного кластерного обслуживания

   - Если вы не знаете идентификатор основного приложения службы кластера, ищите его со следующей командой.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Назначить назначение ролей "Политические исследования данных (Preview)" главному идентификатору приложения кластерного сервиса (значение _aadClientID_ от предыдущего шага) с Azure CLI. Заменить `<subscriptionId>` идентификатор подписки и `<aks engine cluster resource group>` ресурсную группу, в которой находится самоуправляемый кластер Kubernetes AKS Engine.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Дополнение политики Azure

_Дополнение политики Azure_ для Kubernetes соединяет службу политики Azure с контроллером приема Хранителя. Надстройка, которая устанавливается в пространстве имени _kube-системы,_ вводит в себя следующие функции:

- Проверка с помощью политики Azure для заданий в кластере двигателей AKS
- Загрузка и установка сведений о политике, шаблонах ограничений и ограничениях
- Проводит полную проверку соответствия требованиям сканирования кластера AKS Engine
- Отчеты об аудите и сведении соответствия обратно в политику Azure

### <a name="installing-the-add-on"></a>Установка надстройки

Как только предпосылки будут завершены, можно установить дополнение политики Azure. Установка может быть во время создания или обновления цикла AKS Engine или в качестве независимого действия на существующем кластере.

- Установка во время цикла создания или обновления

  Чтобы включить дополнение политики Azure при создании нового самоуправляемого кластера или в качестве обновления существующего кластера, включите определение кластера свойств **аддонов** для AKS Engine.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  Для получения дополнительной информации [AKS Engine cluster definition](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)о, см.

- Установка в существующий кластер с графиками helm

  Используйте следующие шаги для подготовки кластера и установки надстройки:

  1. Установите Привратник в пространство имен _привратника-системы._

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Добавьте метку _управления-плана_ к _kube-системе._ Эта метка исключает аудит стручков и служб _kube-системы_ Gatekeeper и дополнения политики Azure.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Синхронизация данных Kubernetes (Namespace, Pod, Ingress, Service) с OPA.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Для получения дополнительной информации [см.](https://github.com/open-policy-agent/gatekeeper#replicating-data)

  1. Добавьте репо политики Azure в шлем.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Для получения дополнительной информации, [см. Helm Chart - Руководство по быстрому запуску](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Установите надстройку с помощью таблицы helm Chart. Заменить `<subscriptionId>` идентификатор подписки и `<aks engine cluster resource group>` ресурсную группу, в которой находится самоуправляемый кластер Kubernetes AKS Engine.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Для получения дополнительной информации о том, что [Azure Policy Add-on Helm Chart definition](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) устанавливается надстройка Helm Chart, см.

     > [!NOTE]
     > Из-за взаимосвязи между надстройкой Azure Policy и идентификатором группы ресурсов Azure Policy поддерживает только один кластер AKS Engine для каждой группы ресурсов.

Чтобы подтвердить, что установка дополнения была успешной и что стручок _политики лазури_ работает, запустите следующую команду:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Частота валидирования и отчетности

Надстройка проверяется с политикой Azure для изменений в назначениях политики каждые 5 минут. Во время этого цикла обновления надстройка проверяет изменения. Эти изменения вызывают создание, обновление или удаление шаблонов и ограничений ограничений.

> [!NOTE]
> Хотя _у кластерного адмий-мина_ может быть разрешение на внесение изменений в шаблоны и ограничения ограничений, не рекомендуется или поддерживаться для внесения изменений в шаблоны ограничения или ограничения, созданные политикой Azure. Любые внесенные вручную изменения теряются во время цикла обновления.

Каждые 5 минут надстройка требует полного сканирования кластера. После сбора сведений о полном сканировании и любых оценках в реальном времени, сделанных Gatekeeper в отношении попыток изменений в кластере, надстройка сообщает результаты обратно в политику Azure для включения в [детали соответствия,](../how-to/get-compliance-data.md) как и любое назначение политики Azure. В цикле аудита возвращаются только результаты для активных назначений политики. Результаты аудита также можно рассматривать как нарушения, перечисленные в поле состояния неудавшегося ограничения.

## <a name="policy-language"></a>Язык политики

Языковая структура политики Azure для управления AKS Engine соответствует структуре существующих политик. Эффект _EnforceOPAConstraint_ используется для управления кластерами AKS Engine и использует свойства _деталей,_ характерные для работы с [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) и Gatekeeper v3. Для получения подробной информации и примеров см. [EnforceOPAConstraint](effects.md#enforceopaconstraint)

В рамках _подробной информации.constraintTemplate_ и _свойства details.constraint_ в определении политики, политика Azure передает URIs этих [пользовательских определений ресурсов](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) в дополнение. Rego — это язык, который OPA и Gatekeeper поддерживают для проверки запроса в кластер Kubernetes. Поддерживая существующий стандарт управления Kubernetes, Azure Policy позволяет повторно использовать существующие правила и сочетать их с политикой Azure для единого опыта отчетности об облаке. Для получения дополнительной информации, [см. Что такое Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Встроенные политики

Чтобы найти встроенные политики для управления кластером AKS Engine с помощью портала Azure, выполните следующие действия:

1. Запустите службу политики Azure на портале Azure. Выберите **все службы** в левом стеле, а затем ищите и выберите **Политику.**

1. В левом стеле страницы политики Azure выберите **Определения**.

1. Из окна списка категорий используйте **все,** чтобы очистить фильтр, а затем выберите **Kubernetes.**

1. Выберите определение политики, а затем выберите кнопку **«Назначить».**

> [!NOTE]
> При назначении политики Azure для определения AKS Engine **область** должна быть группой ресурсов кластера AKS Engine.

Кроме того, используйте [политику Назначать - Портал](../assign-policy-portal.md) quickstart, чтобы найти и назначить политику AKS Engine. Поиск определения политики AKS Engine вместо образца 'audit vms'.

> [!IMPORTANT]
> Встроенные политики в категории **Kubernetes** предназначены только для использования с AKS Engine.

## <a name="logging"></a>Ведение журнала

### <a name="azure-policy-add-on-logs"></a>Дополнительные журналы политики Azure

В качестве контроллера/контейнера Kubernetes в кластере AZS Engine вжурналев политики Azure ведется журналы.

Для просмотра журналов дополнений политики `kubectl`Azure используйте:

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Привратник журналы

Стручок Gatekeeper, _привратник-контроллер-менеджер-0_, как правило, в `gatekeeper-system` или `kube-system` пространстве имен, но может быть в другом пространстве имен в зависимости от того, как он развернут.

Для просмотра журналов Хранителя `kubectl`прививок используйте:

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Для получения дополнительной информации в документации OPA можно [ознакомиться на информации Debugging Gatekeeper.](https://github.com/open-policy-agent/gatekeeper#debugging)

## <a name="remove-the-add-on"></a>Удалить надстройку

Чтобы удалить надстройку и хранитель прививок политики Azure из кластера AKS Engine, используйте метод, который соответствует тому, как было установлено дополнение:

- Если установлено, установив свойство **аддонов** в определении кластера для AKS Engine:

  Перераспределите определение кластера в AKS Engine после изменения свойства **аддонов** для _политики azure_ на ложное:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Если установлены с helm Диаграммы:

  1. Удалить старые ограничения

     В настоящее время механизм удаления удаления только системы Gatekeeper, он не удаляет любые _ресурсы ConstraintTemplate,_ _constraint_, или _Config,_ которые были созданы пользователем, и не удаляет сопровождающие их _CRD._

     Когда Gatekeeper работает, можно удалить нежелательные ограничения:

     - Удаляем все экземпляры ресурса сограниченным
     - Устранение ресурса _ConstraintTemplate,_ который должен автоматически очистить _CRD_
     - Удаление ресурса _Config_ удаляет финалы на синхронизированных ресурсах

  1. Удаление дополнения политики Azure
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Установить привратник
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Диагностические данные, собранные дополнением политики Azure

Дополнение политики Azure для Kubernetes собирает ограниченные данные кластерной диагностики. Эти диагностические данные имеют жизненно важные технические данные, связанные с программным обеспечением и производительностью. Она используется для:

- Обновляйте добавленную часть политики Azure
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