---
title: Устранение распространенных ошибок
description: Узнайте, как устранять неполадки при создании определений политик, различных пакетах SDK и надстройки для Kubernetes.
ms.date: 12/01/2020
ms.topic: troubleshooting
ms.openlocfilehash: f3667988d527100507d308887338278e1200d454
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511004"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Устранение ошибок с помощью политики Azure

Вы можете столкнуться с ошибками при создании определений политик, работе с пакетом SDK или настройке [политики Azure для](../concepts/policy-for-kubernetes.md) надстройки Kubernetes. В этой статье описываются различные общие ошибки, которые могут возникнуть, и способы их устранения.

## <a name="finding-error-details"></a>Поиск сведений об ошибке

Расположение сведений об ошибке зависит от действия, которое вызывает ошибку.

- При работе с настраиваемой политикой попробуйте использовать ее в портал Azure, чтобы получить linting отзыв о схеме или просмотреть результирующие [данные о соответствии](../how-to/get-compliance-data.md) , чтобы увидеть, как ресурсы были оценены.
- При работе с различными пакетами SDK пакет SDK предоставляет сведения о причинах сбоя функции.
- При работе с надстройкой для Kubernetes Начните с [ведения журнала](../concepts/policy-for-kubernetes.md#logging) в кластере.

## <a name="general-errors"></a>Общие ошибки

### <a name="scenario-alias-not-found"></a>Сценарий: псевдоним не найден

#### <a name="issue"></a>Проблема

Политика Azure использует [псевдонимы](../concepts/definition-structure.md#aliases) для соотнесения со свойствами Azure Resource Manager.

#### <a name="cause"></a>Причина:

В определении политики используется неправильный или несуществующий псевдоним.

#### <a name="resolution"></a>Разрешение

Сначала убедитесь, что свойство диспетчер ресурсов имеет псевдоним. Используйте [расширение политики Azure для Visual Studio Code](../how-to/extension-for-vscode.md), [графа ресурсов Azure](../../resource-graph/samples/starter.md#distinct-alias-values)или пакета SDK, чтобы найти доступные псевдонимы. Если псевдоним для свойства диспетчер ресурсов не существует, создайте запрос в службу поддержки.

### <a name="scenario-evaluation-details-not-up-to-date"></a>Сценарий: сведения об оценке не обновлены

#### <a name="issue"></a>Проблема

Ресурс находится в состоянии "не запущено" или сведения о соответствии не являются актуальными.

#### <a name="cause"></a>Причина:

Применение новой политики или назначения инициативы занимает около 30 минут. Новые или обновленные ресурсы в области действия существующего назначения становятся доступными примерно через 15 минут позже. Стандартная проверка соответствия происходит каждые 24 часа. Дополнительные сведения см. в разделе [триггеры оценки](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Разрешение

Сначала дождитесь необходимого времени для завершения оценки, и результаты обеспечения соответствия будут доступны в портал Azure или пакете SDK. Чтобы запустить новую проверку для оценки с Azure PowerShell или REST API, см. статью [Проверка оценки по требованию](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-compliance-not-as-expected"></a>Сценарий: соответствие не так, как ожидалось

#### <a name="issue"></a>Проблема

Ресурс не находится в состоянии оценки ( _соответствует_ или _не соответствует требованиям_), ожидаемому для этого ресурса.

#### <a name="cause"></a>Причина:

Ресурс не находится в правильной области действия для назначения политики, или определение политики не работает должным образом.

#### <a name="resolution"></a>Разрешение

Выполните следующие действия, чтобы устранить неполадки определения политики.

1. Сначала дождитесь необходимого времени для завершения оценки, и результаты обеспечения соответствия будут доступны в портал Azure или пакете SDK. Чтобы запустить новую проверку для оценки с Azure PowerShell или REST API, см. статью [Проверка оценки по требованию](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Убедитесь, что параметры назначения и область назначения заданы правильно.
1. Проверьте [режим определения политики](../concepts/definition-structure.md#mode):
   - Режим "все" для всех типов ресурсов.
   - Режим "индексированный", если определение политики проверяет наличие тегов или расположения.
1. Убедитесь, что область действия ресурса не исключена или [не](../concepts/exemption-structure.md) [исключается](../concepts/assignment-structure.md#excluded-scopes) .
1. Если соответствие для назначения политики показывает `0/0` ресурсы, ресурсы не были определены как применимые в области назначения. Проверьте определение политики и область назначения.
1. Для несоответствующих ресурсов, которые должны быть совместимы, проверьте [причины несоответствия](../how-to/determine-non-compliance.md). Сравнение определения с вычисленным значением свойства указывает, почему ресурс не соответствует требованиям.
   - Если **целевое значение** неверно, измените определение политики.
   - Если **Текущее значение** не подходит, проверьте полезные данные ресурса с помощью `resources.azure.com` .
1. Проверить [Устранение неполадок: принудительное применение не так, как ожидается](#scenario-enforcement-not-as-expected) , для других распространенных проблем и решений.

Если у вас по-прежнему возникли проблемы с дубликатом и настраиваемым определением встроенной политики или пользовательским определением, создайте запрос в службу поддержки в разделе **Создание политики** для правильной маршрутизации проблемы.

### <a name="scenario-enforcement-not-as-expected"></a>Сценарий: принудительное применение не так, как ожидалось

#### <a name="issue"></a>Проблема

Ресурс, который должен обрабатываться политикой Azure, не содержит записей в [журнале действий Azure](../../../azure-monitor/platform/platform-logs-overview.md).

#### <a name="cause"></a>Причина:

Назначение политики настроено для [Енфорцементмоде](../concepts/assignment-structure.md#enforcement-mode) _отключено_. Если режим принудительного применения отключен, то этот результат политики не применяется, и в журнале действий нет записи.

#### <a name="resolution"></a>Разрешение

Выполните следующие действия, чтобы устранить проблему принудительного применения назначения политики.

1. Сначала дождитесь необходимого времени для завершения оценки, и результаты обеспечения соответствия будут доступны в портал Azure или пакете SDK. Чтобы запустить новую проверку для оценки с Azure PowerShell или REST API, см. статью [Проверка оценки по требованию](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Убедитесь, что параметры назначения и область назначения заданы правильно и что **Енфорцементмоде** _включен_.
1. Проверьте [режим определения политики](../concepts/definition-structure.md#mode):
   - Режим "все" для всех типов ресурсов.
   - Режим "индексированный", если определение политики проверяет наличие тегов или расположения.
1. Убедитесь, что область действия ресурса не исключена или [не](../concepts/exemption-structure.md) [исключается](../concepts/assignment-structure.md#excluded-scopes) .
1. Убедитесь, что полезные данные ресурса соответствуют логике политики. Это можно сделать, [записывая трассировку Har](../../../azure-portal/capture-browser-trace.md) или просмотрев свойства шаблона ARM.
1. Проверить [Устранение неполадок: соответствие требованиям](#scenario-compliance-not-as-expected) для других распространенных проблем и решений.

Если у вас по-прежнему возникли проблемы с дубликатом и настраиваемым определением встроенной политики или пользовательским определением, создайте запрос в службу поддержки в разделе **Создание политики** для правильной маршрутизации проблемы.

### <a name="scenario-denied-by-azure-policy"></a>Сценарий: отклонено политикой Azure

#### <a name="issue"></a>Проблема

Для создания или обновления ресурса отказано.

#### <a name="cause"></a>Причина:

Назначение политики области, в которой новый или обновленный ресурс соответствует критериям определения политики с применением [запрета](../concepts/effects.md#deny) . Ресурсы собрания эти определения не могут быть созданы или обновлены.

#### <a name="resolution"></a>Разрешение

Сообщение об ошибке из назначения «запретить политику» включает в себя определение политики и идентификаторы назначения политики. Если информация об ошибке в сообщении отсутствует, она также доступна в [журнале действий](../../../azure-monitor/platform/activity-log.md#view-the-activity-log). Используйте эти сведения для получения дополнительных сведений об ограничениях ресурсов и настройке свойств ресурсов в запросе в соответствии с допустимыми значениями.

## <a name="template-errors"></a>Ошибки шаблона

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Сценарий: Поддерживаемые функцией политики функции, обрабатываемые шаблоном

#### <a name="issue"></a>Проблема

Политика Azure поддерживает несколько Azure Resource Manager функций и функций шаблона (шаблона ARM), которые доступны только в определении политики. Диспетчер ресурсов обрабатывает эти функции как часть развертывания, а не как часть определения политики.

#### <a name="cause"></a>Причина:

Использование поддерживаемых функций, таких как `parameter()` или `resourceGroup()` , приводит к обработке результата функции во время развертывания, вместо того чтобы использовать функцию для определения политики и для обработки обработчика политики Azure.

#### <a name="resolution"></a>Разрешение

Чтобы передать функцию через, чтобы она была частью определения политики, заэкранированайте всю строку с `[` таким образом, чтобы свойство было похоже на `[[resourceGroup().tags.myTag]` . Escape-символ заставляет диспетчер ресурсов считать значение строкой при обработке шаблона. Затем политика Azure помещает функцию в определение политики, что позволяет использовать ее динамически, как и ожидалось. Дополнительные сведения см. [в разделе синтаксис и выражения в шаблонах Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-for-kubernetes-installation-errors"></a>Надстройка для ошибок установки Kubernetes

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>Сценарий: Установка с использованием диаграммы Helm завершается сбоем при пароле

#### <a name="issue"></a>Проблема

`helm install azure-policy-addon`Команда завершается с одним из следующих сообщений:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Причина:

Созданный пароль включает запятую ( `,` ), на которой Helm диаграмма.

#### <a name="resolution"></a>Разрешение

`,`При запуске `helm install azure-policy-addon` с помощью обратной косой черты () в качестве значения пароля используется escape-последовательность () `\` .

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>Сценарий: Установка с использованием диаграммы Helm не выполняется, так как имя уже существует

#### <a name="issue"></a>Проблема

`helm install azure-policy-addon`Команда завершается со следующим сообщением:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Причина:

Диаграмма Helm с именем `azure-policy-addon` уже установлена или частично установлена.

#### <a name="resolution"></a>Разрешение

Следуйте указаниям, чтобы [удалить политику Azure для надстройки Kubernetes](../concepts/policy-for-kubernetes.md#remove-the-add-on), а затем повторите `helm install azure-policy-addon` команду.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Сценарий: назначенные пользователем удостоверения виртуальной машины Azure заменяются управляемыми идентификаторами, назначенными системой

#### <a name="issue"></a>Проблема

После назначения инициатив политики гостевой конфигурации для аудита параметров внутри компьютеров назначенные пользователю управляемые удостоверения, назначенные компьютеру, больше не назначаются. Назначается только управляемое системой удостоверение.

#### <a name="cause"></a>Причина:

Определения политик, ранее использовавшиеся в настройках гостевой конфигурации DeployIfNotExists, гарантируют, что назначенное системой удостоверение назначено компьютеру, но также были удалены назначенные пользователю назначения удостоверений.

#### <a name="resolution"></a>Разрешение

Определения, которые ранее вызывали эту неполадку, отображаются как \[ устаревшие \] и заменяются определениями политик, которые управляют необходимыми компонентами без удаления назначенного пользователем управляемого удостоверения. Требуется выполнить шаг вручную. Удалите все существующие назначения политик, помеченные как \[ устаревшие, \] и замените их обновленной инициативой и определениями политик, имена которых совпадают с именами исходных файлов.

Подробное описание см. в следующей записи блога:

[Важное изменение, выпущенное для политик аудита настройки гостя](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

## <a name="add-on-for-kubernetes-general-errors"></a>Общая ошибка надстройки для Kubernetes

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-egress-restrictions"></a>Сценарий: надстройка не может связаться с конечной точкой службы политики Azure из-за ограничений исходящего трафика

#### <a name="issue"></a>Проблема

Надстройка не может связаться с конечной точкой службы политики Azure и возвращает одну из следующих ошибок:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Причина:

Эта проблема возникает, когда исходящий трафик кластера заблокирован.

#### <a name="resolution"></a>Разрешение

Убедитесь, что домены и порты в следующих статьях открыты:

- [Необходимые правила исходящей сети и FQDN для кластеров AKS](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Установка надстройки политики Azure для Kubernetes с поддержкой дуги Azure (Предварительная версия)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-aad-pod-identity-configuration"></a>Сценарий: надстройке не удается связаться с конечной точкой службы политики Azure из-за конфигурации AAD-Pod-Identity

#### <a name="issue"></a>Проблема

Надстройка не может связаться с конечной точкой службы политики Azure и возвращает одну из следующих ошибок:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Причина:

Эта ошибка возникает, если в кластере установлена _надстройка Add-Pod-Identity_ , а _KUBE-System_ не исключены в _AAD-Pod-Identity_.

Модули управления " _AAD-Pod-Identity_ " с узлом компонента идентификации (NMI) изменяют узлы "Iptables" для перехвата вызовов к конечной точке метаданных экземпляра Azure. Такая настройка означает, что любой запрос, сделанный в конечной точке метаданных, перехватывается функцией NMI, даже если в Pod не используется _AAD-Pod-Identity_.
**Азуреподидентитексцептион** CRD можно настроить для информирования _AAD-Pod_ о том, что любые запросы к конечной точке метаданных, создаваемые из Pod, которые соответствуют меткам, определенным в CRD, должны быть прокси без обработки в NMI.

#### <a name="resolution"></a>Разрешение

Исключите системные модули с `kubernetes.azure.com/managedby: aks` меткой в пространстве имен _KUBE-System_ в _AAD-Pod-Identity_ , настроив **азуреподидентитексцептион** CRD.

Дополнительные сведения см. в разделе [Отключение удостоверения Pod для конкретного Pod или приложения](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).

Сведения о настройке исключения см. в следующем примере:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>Сценарий: поставщик ресурсов не зарегистрирован

#### <a name="issue"></a>Проблема

Надстройка может получить доступ к конечной точке службы политики Azure, но видит одну из следующих ошибок в журналах надстроек:

```
The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
https://aka.ms/policy-register-subscription for how to register subscriptions.
```

или

```
policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
Code="InternalServerError" Message="Encountered an internal server error."
```

#### <a name="cause"></a>Причина:

`Microsoft.PolicyInsights`Поставщик ресурсов не зарегистрирован и должен быть зарегистрирован для надстройки, чтобы получать определения политик и возвращать данные о соответствии.

#### <a name="resolution"></a>Разрешение

Зарегистрируйте `Microsoft.PolicyInsights` поставщик ресурсов в подписке кластера. Инструкции см. [в разделе Регистрация поставщика ресурсов](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

### <a name="scenario-the-subscription-is-disabled"></a>Сценарий: Подписка отключена.

#### <a name="issue"></a>Проблема

Надстройка может получить доступ к конечной точке службы политики Azure, но видит следующую ошибку:

```
The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.
```

#### <a name="cause"></a>Причина:

Эта ошибка означает, что подписка была определена как проблематичная, а флаг функции `Microsoft.PolicyInsights/DataPlaneBlocked` был добавлен для блокировки подписки.

#### <a name="resolution"></a>Разрешение

`azuredg@microsoft.com`Чтобы исследовать и устранить эту проблему, обратитесь к группе функций.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

- Получите ответы от экспертов по [Microsoft Q&а](/answers/topics/azure-policy.html).
- Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
- Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.
