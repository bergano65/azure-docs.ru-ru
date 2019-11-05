---
title: Сведения о политике Azure для AKS Engine
description: Узнайте, как политика Azure использует Кустомресаурцедефинитионс и открывает агент политики из привратника v3 для управления кластерами с помощью AKS Engine.
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 71f3f26b9ea7f24d674c911e18c785b0798a072c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512136"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Общие сведения о политике Azure для AKS Engine

Политика Azure интегрируется с [AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/README.md)— системой, которая предоставляет удобные средства для быстрой загрузки самоуправляемого кластера Kubernetes в Azure. Такая интеграция позволяет централизованно и согласованно выполнять масштабирование на самостоятельных кластерах AKS Engine. Благодаря расширению использования модуля [Open Policy Agent](https://www.openpolicyagent.org/) (Непрозра) [привратник](https://github.com/open-policy-agent/gatekeeper) v3 (Beta), _веб-перехватчик контроллера допуска_ для Kubernetes, политика Azure позволяет управлять состоянием соответствия ресурсов Azure и подсистемой AKS и сообщать о нем. кластеры с самостоятельным управлением из одного места.

> [!NOTE]
> Политика Azure для AKS Engine находится в общедоступной предварительной версии и не имеет соглашения об уровне обслуживания. Привратник v3 находится в бета-версии и поддерживается сообществом с открытым исходным кодом. Служба поддерживает только определения встроенных политик и один кластер AKS Engine для каждой группы ресурсов, настроенной с помощью субъекта-службы.

> [!IMPORTANT]
> Чтобы получить поддержку для политики Azure для ядра AKS, AKS Engine или привратника v3, создайте [новую ошибку](https://github.com/Azure/aks-engine/issues/new/choose) в репозитории GITHUB модуля AKS.

## <a name="overview"></a>Обзор

Чтобы включить и использовать политику Azure для AKS Engine с самостоятельно управляемым кластером Kubernetes в Azure, выполните следующие действия.

- [Предварительные требования](#prerequisites)
- [Установка надстройки политики Azure](#installing-the-add-on)
- [Назначение определения политики для ядра AKS](#built-in-policies)
- [Ожидание проверки](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Технические условия

Прежде чем устанавливать надстройку политики Azure или включить какие-либо функции службы, ваша подписка должна включить поставщик ресурсов **Microsoft. полициинсигхтс** и создать назначение роли для субъекта-службы кластера. 

1. Чтобы включить поставщик ресурсов, выполните действия, описанные в разделе [поставщики ресурсов и типы](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal) , либо выполните команду Azure CLI или Azure PowerShell.

   - Azure CLI

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

1. Создание назначения роли для субъекта-службы кластера

   - Если вы не знакомы с ИДЕНТИФИКАТОРом приложения субъекта-службы кластеров, найдите его с помощью следующей команды.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Назначьте роль "средство записи данных о политике (Предварительная версия)" для идентификатора приложения-участника службы кластеров (значение _аадклиентид_ из предыдущего шага) с Azure CLI. Замените `<subscriptionId>` ИДЕНТИФИКАТОРом подписки и `<aks engine cluster resource group>` с группой ресурсов, в которой находится кластер Kubernetes с самоуправлением AKS Engine.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Надстройка политики Azure

_Надстройка политики Azure_ для Kubernetes подключает службу политики Azure к контроллеру допуска привратника. Надстройка, устанавливаемая в пространство имен _KUBE-System_ , вводит следующие функции:

- Проверяет наличие политики Azure для назначений в кластере AKS Engine
- Скачивает и устанавливает сведения о политике, шаблоны ограничений и ограничения.
- Выполнение проверки соответствия полной проверки на кластере AKS Engine
- Сообщает сведения об аудите и соответствии политике Azure

### <a name="installing-the-add-on"></a>Установка надстройки

После завершения предварительных требований можно установить надстройку политики Azure. Установка может выполняться во время цикла создания или обновления ядра AKS или как независимое действие в существующем кластере.

- Установка во время цикла создания или обновления

  Чтобы включить надстройку политики Azure во время создания нового самостоятельно управляемого кластера или в качестве обновления существующего кластера, включите в него определение кластера свойств **надстроек** для ядра AKS.

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

  Дополнительные сведения о см. в статье [Определение кластера AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)с помощью внешней программы.

- Установка в существующем кластере с Helm диаграммами

  Чтобы подготовить кластер и установить надстройку, выполните следующие действия.

  1. Установите привратник в пространство имен _привратника_ .

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Добавление метки _плоскости управления_ в _KUBE-System_. Эта метка исключает аудит _KUBE-систем_ и служб в модулях и надстройке политики Azure.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Синхронизация данных Kubernetes (пространство имен, Pod, входящий трафик, служба) с помощью НЕПРОЗРА.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Дополнительные сведения см. в разделе [непрозра-реплицирует данные](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Добавьте репозиторий политик Azure в Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Дополнительные сведения см. в разделе [Helm Chart — краткое руководство](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Установите надстройку с Helm диаграммой. Замените `<subscriptionId>` ИДЕНТИФИКАТОРом подписки и `<aks engine cluster resource group>` с группой ресурсов, в которой находится кластер Kubernetes с самоуправлением AKS Engine.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Дополнительные сведения о том, что такое надстройка Helm, см. в статье [Определение диаграммы для надстройки Azure Policy Helm](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) на сайте GitHub.

     > [!NOTE]
     > Из-за связи между надстройкой политики Azure и идентификатором группы ресурсов политика Azure поддерживает только один кластер AKS Engine для каждой группы ресурсов.

Чтобы проверить успешность установки надстройки и запустить модуль _политики Azure_ , выполните следующую команду:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Периодичность проверки и отчетности

Надстройка проверяет в политике Azure изменения назначений политик каждые 5 минут. Во время этого цикла обновления надстройка проверяет наличие изменений. Эти изменения активируют создание, обновление или удаление шаблонов ограничений и ограничений.

> [!NOTE]
> Хотя _Администратор кластера_ может иметь разрешение на внесение изменений в шаблоны ограничений и ограничения, не рекомендуется или не поддерживается вносить изменения в шаблоны ограничений или ограничения, созданные политикой Azure. Все внесенные вручную изменения теряются во время цикла обновления.

Каждые 5 минут надстройка вызывает полную проверку кластера. После сбора подробных сведений о полной проверке и любых оценках в реальном времени с помощью привратника попыток внести изменения в кластер, эта надстройка выводит результаты обратно в политику Azure для включения в [сведения о соответствии](../how-to/get-compliance-data.md) , например любое назначение политики Azure. Во время цикла аудита возвращаются только результаты активных назначений политик. Результаты аудита также могут рассматриваться как нарушения, перечисленные в поле состояние ограничения, завершившееся сбоем.

## <a name="policy-language"></a>Язык политики

Языковая структура политики Azure для управления ядром AKS соответствует существующим политикам. Действие _енфорцеопаконстраинт_ используется для управления кластерами AKS Engine и принимает свойства _сведений_ , относящиеся к работе с [непрозра Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) и привратником v3. Дополнительные сведения и примеры см. в разделе [енфорцеопаконстраинт](effects.md#enforceopaconstraint) Effect.

В рамках свойств _Details. констраинттемплате_ и _Details. Constraint_ в определении политики Политика Azure передает URI этих [кустомресаурцедефинитионс](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) в надстройку. Рего — это язык, который поддержка НЕПРОЗРА и привратника для проверки запроса к кластеру Kubernetes. Благодаря поддержке существующего стандарта для управления Kubernetes, политика Azure позволяет повторно использовать существующие правила и связывать их с политикой Azure для единого создания отчетов о соответствии с облаком. Дополнительные сведения см. в разделе [что такое Рего?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Встроенные политики

Чтобы найти встроенные политики для управления кластером AKS Engine с помощью портал Azure, выполните следующие действия.

1. Запустите службу политики Azure в портал Azure. Выберите **все службы** на левой панели, а затем найдите и выберите **Политика**.

1. В левой области страницы политика Azure выберите **определения**.

1. В раскрывающемся списке Категория **выберите команду выбрать все** , чтобы очистить фильтр, а затем выберите **Kubernetes**.

1. Выберите определение политики, а затем нажмите кнопку **назначить** .

> [!NOTE]
> При назначении определения ядра политики Azure для AKS **область** должна быть группой ресурсов кластера AKS Engine.

Кроме того, для поиска и назначения политики AKS Engine используйте краткое руководство по [назначению политики](../assign-policy-portal.md) с помощью портала. Выполните поиск определения политики ядра AKS вместо примера "Audit VMS".

> [!IMPORTANT]
> Встроенные политики в категории **Kubernetes** предназначены только для использования с подсистемой AKS.

## <a name="logging"></a>Ведение журналов

### <a name="azure-policy-add-on-logs"></a>Журналы надстроек для политики Azure

Как контроллер или контейнер Kubernetes, надстройка политики Azure хранит журналы в кластере AKS Engine.

Чтобы просмотреть журналы надстроек для политики Azure, используйте `kubectl`:

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Журналы привратника

Привратник Pod _-Controller-Manager-0_обычно находится в `gatekeeper-system` или `kube-system` пространстве имен, но может находиться в другом пространстве имен в зависимости от того, как оно развернуто.

Чтобы просмотреть журналы привратника, используйте `kubectl`:

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Дополнительные сведения см. в разделе [Отладка привратника](https://github.com/open-policy-agent/gatekeeper#debugging) в документации по непрозра.

## <a name="remove-the-add-on"></a>Удаление надстройки

Чтобы удалить надстройку и привратник политики Azure из кластера AKS Engine, используйте метод, который соответствует способу установки надстройки:

- Если установка выполняется путем установки свойства **надстроек** в определении кластера для AKS Engine:

  Повторно разверните определение кластера в AKS Engine после изменения свойства " **надстройки** " для _Azure-Policy_ на false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- При установке с Helm диаграммами:

  1. Удалить старые ограничения

     В настоящее время механизм удаления удаляет только систему привратника, но не удаляет все ресурсы _констраинттемплате_, _constraint_или _config_ , созданные пользователем, а также не удаляет сопутствующие _КРДС_ .

     Когда привратник запущен, можно удалить нежелательные ограничения, выполнив следующие действия.

     - Удаление всех экземпляров ресурса ограничения
     - Удаление ресурса _констраинттемплате_ , который должен автоматически очищать _CRD_
     - При удалении ресурса _конфигурации_ методы завершения для синхронизированных ресурсов удаляются.

  1. Удаление надстройки политики Azure
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Удалить привратника
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Диагностические данные, собираемые надстройкой политики Azure

Надстройка политики Azure для Kubernetes собирает ограниченные диагностические данные кластера. Эти диагностические данные являются важнейшими техническими данными, относящимися к программному обеспечению и производительности. Он используется следующими способами:

- Обновление надстройки политики Azure
- Обеспечьте надежную, надежную, производительную надстройку политики Azure
- Улучшите надстройку политики Azure с помощью статистического анализа использования надстройки

Сведения, собираемые надстройкой, не являются личными данными. В настоящее время собраны следующие сведения:

- Версия агента надстройки политики Azure
- Тип кластера
- Регион кластера
- Группа ресурсов кластера
- Идентификатор ресурса кластера
- Идентификатор подписки кластера
- ОС кластера (пример: Linux)
- Город кластера (пример: Сиэтл)
- Штат или провинция кластера (пример: Вашингтон)
- Страна или регион кластера (например: США)
- Исключения и ошибки, обнаруженные надстройкой политики Azure во время установки агента при оценке политики
- Число политик привратника, не установленных надстройкой политики Azure

## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите примеры в [примерах политики Azure](../samples/index.md).
- См. дополнительные сведения о [структуре определения Политики Azure](definition-structure.md).
- Изучите [сведения о действии политик](effects.md).
- Узнайте, как [программно создавать политики](../how-to/programmatically-create.md).
- Узнайте, как [получить данные о соответствии](../how-to/getting-compliance-data.md).
- Узнайте, как [исправлять несоответствующие ресурсы](../how-to/remediate-resources.md).
- Дополнительные сведения о группе управления см. в статье [Упорядочивание ресурсов с помощью групп управления Azure](../../management-groups/overview.md).