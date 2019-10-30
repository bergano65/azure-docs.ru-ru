---
title: Получение данных о соответствии политике
description: Соответствие определяется оценками и действиями Политики Azure. Узнайте о том, как получить подробные сведения о соответствии.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 47258f27f44b6a21c5da72e4631591e695024400
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053281"
---
# <a name="get-compliance-data-of-azure-resources"></a>Получение данных о соответствии ресурсов Azure

Одним из наибольших преимуществ Политики Azure является анализ и контроль, который эта служба выполняет над ресурсами в подписке или [группе управления](../../management-groups/overview.md) подписками. Этот контроль может быть реализован разными способами, такими как предотвращение создания ресурсов в неправильном месте, принудительное использование общих и согласованных тегов, а также аудит существующих ресурсов на предмет соответствующих конфигураций и параметров. Во всех случаях данные создаются политикой Azure, чтобы вы понимали состояние соответствия вашей среды.

Существует несколько способов доступа к информации о соответствии, которая создается при назначении политик и инициатив:

- с помощью [портала Azure](#portal);
- с помощью скриптов [командной строки](#command-line).

Прежде чем переходить к способам создания отчетов о соответствии, давайте рассмотрим, когда обновляются данные о соответствии, а также с какой частотой и какие события запускают цикл оценки.

> [!WARNING]
> Если состояние соответствия указывается как **не зарегистрировано**, убедитесь, что поставщик ресурсов **Microsoft. полициинсигхтс** зарегистрирован и что пользователь имеет соответствующие разрешения управления доступом на основе ролей (RBAC), как описано в [RBAC в Политика Azure](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Триггеры оценки

Результаты завершенного цикла оценки отражаются в поставщике ресурсов `Microsoft.PolicyInsights` с помощью операций `PolicyStates` и `PolicyEvents`. Дополнительные сведения об операциях REST API Azure Policy Insights см. в статье [Azure Policy Insights](/rest/api/policy-insights/).

Оценка назначенных политик и инициатив выполняется в результате разных событий:

- Определенной области назначается новая политика или инициатива. Применение назначения к заданной области занимает около 30 минут. После применения начинается цикл оценки для ресурсов в соответствующей области относительно новой назначенной политики или инициативы. В зависимости от действий, реализуемых этой политикой или инициативой, ресурсы отмечаются как соответствующие или несоответствующие. Оценка масштабной политики или инициативы относительно большой области ресурсов может занять много времени. Поэтому сведений об ожидаемом времени завершения цикла оценки нет. После этого обновленные результаты оценки соответствия становятся доступными на портале и в пакетах SDK.

- Политика или инициатива, уже назначенная определенной области, является обновленной. Цикл и время оценки в этом сценарии аналогичны новому назначению области.

- Ресурс развертывается в области с назначением с помощью Resource Manager, REST, Azure CLI или Azure PowerShell. В этом сценарии событие действия политики (присоединение, аудит, отказ, развертывание) и данные о состоянии соответствия отдельного ресурса становятся доступными на портале и в пакетах SDK примерно через 15 минут. Это событие не вызывает оценку других ресурсов.

- Стандартный цикл оценки соответствия. Каждые 24 часа назначения автоматически повторно оцениваются. Оценка масштабной политики или инициативы для большой области ресурсов может занять много времени. Поэтому сведений об ожидаемом времени завершения цикла оценки нет. После этого обновленные результаты оценки соответствия становятся доступными на портале и в пакетах SDK.

- Управляемый ресурс обновляет поставщик ресурсов [гостевой конфигурации](../concepts/guest-configuration.md), добавляя сведения о соответствии.

- Проверка по запросу.

### <a name="on-demand-evaluation-scan"></a>Проверка оценки по запросу

Оценочное сканирование для подписки или группы ресурсов можно запустить с помощью вызова REST API. Эта проверка является асинхронным процессом. Это означает, что запускающая процесс конечная точка REST не обязана ждать его завершения. Вместо этого она предоставляет URI для получения состояния выполняемой оценки.

В каждом универсальном коде ресурса (URI) REST API есть переменные, которые необходимо заменить собственными значениями:

- `{YourRG}` — замените это значение именем своей группы ресурсов.
- `{subscriptionId}` — замените это значение идентификатором своей подписки

Сканирование поддерживает оценку ресурсов в подписке или группе ресурсов. Запустите сканирование для области через команду **POST** REST API, используя следующие структуры универсального кода ресурса (URI):

- Subscription

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- группа ресурсов.

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

Этот вызов возвращает состояние **202 — принято**. В заголовок ответа включается свойство **Location** в следующем формате:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` создается для запрошенной области статически. Если эта область уже выполняет проверку по требованию, новое сканирование не запускается. Вместо этого новому запросу для проверки состояния предоставляется тот же URI, указанные в свойстве **Location** (`{ResourceContainerGUID}`). Команда **GET** REST API, отправленная на URI, указанный в свойстве **Location**, возвращает состояние **202 — принято**, пока продолжается выполнение оценки. Когда оценочное сканирование завершается, возвращается состояние **200 — ОК**. Вот пример текста в формате JSON, который передается в ответе после завершения проверки:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Как работает соответствие

В назначении ресурс **не соответствует требованиям**, если он не соответствует правилам политики или инициативы.
В следующей таблице показано, как действуют разные политики в сочетании с оценкой условий для определения итогового состояния соответствия.

| Состояние ресурса | Результат | Оценка политики | Состояние соответствия |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Да | Не соответствует |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Нет | Соответствие нормативам |
| Новинка | Audit, AuditIfNotExist\* | Да | Не соответствует |
| Новинка | Audit, AuditIfNotExist\* | Нет | Соответствие нормативам |

\*Для эффектов Append, DeployIfNotExist и AuditIfNotExist требуется, чтобы оператор IF имел значение TRUE.
Эффекты также требуют, чтобы условие существования FALSE было несоответствующим. Когда установлено значение TRUE, условие IF запускает оценку условия существования для связанных ресурсов.

Например, предположим, что у вас есть группа ресурсов ContosoRG с некоторыми учетными записями хранения (выделены красным цветом), которые доступны в общедоступных сетях.

![Учетные записи хранения, доступные в общедоступных сетях](../media/getting-compliance-data/resource-group01.png)

В этом примере необходимо опасаться угроз безопасности. Теперь, когда вы создали назначение политики, оно оценивается для всех учетных записей хранения в группе ресурсов ContosoRG. Оно проверяет три несоответствующие учетные записи хранения, соответственно меняя их состояние на **Не соответствует**.

![Прошедшие аудит несоответствующие учетные записи хранения](../media/getting-compliance-data/resource-group03.png)

Помимо состояний **Соответствует** и **Не соответствует**, к политикам и ресурсам применяются три других состояния:

- **Конфликт**: существует две или более политики с конфликтующими правилами. Например, две политики, добавив один и тот же тег с разными значениями.
- **Не запущено**: цикл оценки для политики или ресурса не запущен.
- **Не зарегистрировано**: поставщик ресурсов политики Azure не зарегистрирован, или учетная запись не имеет разрешения на чтение данных соответствия.

Политика Azure использует поля **тип** и **имя** в определении, чтобы определить, является ли ресурс соответствующим. Если ресурс совпадает, он считается применимым и будет иметь состояние **Соответствует** или **Не соответствует**. Если **Тип** или **Имя** является единственным свойством в определении, приемлемыми и подлежащими оценке считаются все ресурсы.

Процент соответствия требованиям определяется путем деления количества **соответствующих** ресурсов на _общее количество ресурсов_.
_Общее количество ресурсов_ определяется как сумма **соответствующих**, **несоответствующих** и **конфликтующих** ресурсов. Общее соответствие — это сумма разных **соответствующих** ресурсов, деленная на сумму всех различных ресурсов. На приведенном ниже рисунке показано 20 различных ресурсов, которые являются применимыми, и только один является **несоответствующим**. Общее соответствие ресурсов составляет 95 % (19 из 20).

![Пример соответствия политики на странице соответствия требованиям](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Microsoft Azure

На портале Azure можно ознакомиться с процессом визуализации и оценки состояния соответствия в вашей среде. На странице **Политика** параметр **Обзор** предоставляет сведения о соответствии для доступных областей в рамках соответствия для политик и инициатив. В дополнение к состоянию соответствия и числу на каждое назначение, в нем содержится диаграмма, отображающая соответствие за последние семь дней. На странице **Соответствие** содержится большая часть этой информации (за исключением диаграммы), а также предоставляются дополнительные возможности фильтрации и сортировки.

![Пример страницы соответствия политикам Azure](../media/getting-compliance-data/compliance-page.png)

Так как политика или инициатива может быть назначена разным областям, в таблице предусмотрена область для каждого назначения и тип определения, назначенный этой области. Здесь также отображается число несоответствующих ресурсов и политик для каждого назначения. Щелкнув политику или инициативу в таблице, можно получить подробные сведения о соответствии для указанного назначения.

![Пример страницы сведений о соответствии политики Azure](../media/getting-compliance-data/compliance-details.png)

В списке ресурсов на вкладке **Соответствие ресурсов** отображается состояние оценки существующих ресурсов для текущего назначения. На вкладке по умолчанию установлено значение **Не соответствует**, но можно выполнить фильтрацию.
События (присоединение, аудит, отказ, развертывание), инициируемые запросом для создания ресурса, отображаются на вкладке **События**.

![Пример событий соответствия политикам Azure](../media/getting-compliance-data/compliance-events.png)

Щелкните правой кнопкой мыши в строке события, о котором нужно собрать подробные сведения, и выберите **Показать журналы действий**. Откроется страница журнала действий с предварительно отфильтрованными результатами поиска, где отображаются подробные сведения о назначениях и событиях. В журнале действий содержатся дополнительный контекст и сведения об этих событиях.

![Пример журнала действий соответствия политикам Azure](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Общие сведения о несоответствии

<a name="change-history-preview"></a>

Если ресурсы определены **как несовместимые, существует**множество возможных причин. Чтобы определить причину, по которой ресурс не **соответствует требованиям** , или найти ответственного за изменение, см. раздел [Определение несоответствия](./determine-non-compliance.md).

## <a name="command-line"></a>Командная строка

Те же сведения, которые доступны на портале, можно получить с помощью REST API (включая [ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell и Azure CLI (Предварительная версия).
Полные сведения о REST API см. в справочнике по [политикам Azure](/rest/api/policy-insights/) . На страницах справки по REST API есть зеленая кнопка "Попробовать" для каждой операции, которую можно опробовать непосредственно в браузере.

Используйте ARMClient или аналогичное средство для управления проверкой подлинности в Azure для REST API примеров.

### <a name="summarize-results"></a>Суммирование результатов

С помощью REST API формирование сводных данных можно выполнить по контейнеру, определению или назначению. Ниже приведен пример формирования сводных данных на уровне подписки с помощью [сводки по подписке](/rest/api/policy-insights/policystates/summarizeforsubscription)Azure Policy Insight.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

В выходных данных будет представлена сводка по подписке. В следующем примере вывода сводные данные о соответствии содержатся в разделах **value.results.nonCompliantResources** и **value.results.nonCompliantPolicies**. Этот запрос предоставляет дополнительные сведения, включая каждое назначение, входящее в число несоответствующих, а также сведения об определении для каждого назначения. Каждый объект политики в иерархии предоставляет идентификатор **queryResultsUri**, который позволяет получить дополнительные сведения на этом уровне.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Запрос ресурсов

В примере выше значение **value.policyAssignments.policyDefinitions.results.queryResultsUri** содержало пример Uri для всех несоответствующих ресурсов для заданного определения политики. В значении **$filter** для IsCompliant задано значение false, PolicyAssignmentId указывается для определения политики, а затем указывается PolicyDefinitionId. PolicyAssignmentId включается в фильтр, так как PolicyDefinitionId может существовать в нескольких назначениях политик или инициатив с разными областями. Указав как PolicyAssignmentId, так и PolicyDefinitionId, мы можем явно определить ожидаемые результаты. Ранее мы использовали значение **latest** для параметра PolicyStates, что автоматически делает временное окно **from** и **to** равным последним 24 часам.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Приведенный ниже пример усечен к одному несоответствующему ресурсу для краткости. Подробный ответ имеет несколько фрагментов данных о ресурсе, политике или инициативе и назначении. Здесь вы также можете увидеть, какие параметры назначения были переданы в определение политики.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Просмотреть события

После создания или обновления ресурса создается результат оценки политики. Такие результаты называются _событиями политики_. Чтобы просмотреть последние события политики, связанные с подпиской, используйте следующий URI.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

Результаты должны выглядеть примерно так:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Дополнительные сведения о запросах событий политики см. в статье Справочник по [событиям политики Azure](/rest/api/policy-insights/policyevents) .

### <a name="azure-powershell"></a>Azure PowerShell

Модуль Azure PowerShell для политики Azure доступен на коллекция PowerShell с именем [AZ. полициинсигхтс](https://www.powershellgallery.com/packages/Az.PolicyInsights).
Используя PowerShellGet, вы можете установить модуль с помощью `Install-Module -Name Az.PolicyInsights` (установите последнюю версию [Azure PowerShell](/powershell/azure/install-az-ps)):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

Модуль содержит следующие командлеты.

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Пример. Получение сводки состояния для приоритетной назначенной политики с наибольшим числом несоответствующих ресурсов.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Пример. Получение записи состояния для последнего оцененного ресурса (по умолчанию — по меткам времени в порядке убывания).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Пример. Получение сведений для всех несоответствующих ресурсов виртуальной сети.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Пример. Получение событий, связанных с несоответствующими ресурсами виртуальной сети и произошедших после указанной даты.

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

Поле **PrincipalOid** может использоваться для получения определенного пользователя с помощью командлета Azure PowerShell `Get-AzADUser`. Замените **{principalOid}** ответом, полученным в предыдущем примере.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Журналы Azure Monitor

Если у вас есть [Рабочая область log Analytics](../../../log-analytics/log-analytics-overview.md) с `AzureActivity` из [решения аналитика журнала действий](../../../azure-monitor/platform/activity-log-collect.md) , привязанного к вашей подписке, можно также просмотреть результаты несоответствия в цикле оценки с помощью простых запросов Kusto и таблицы `AzureActivity`. С учетом подробных сведений о несоответствии в журналах Azure Monitor вы также можете настроить оповещения для отслеживания несоответствия.


![Соответствие политики Azure с помощью журналов Azure Monitor](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите примеры в [примерах политики Azure](../samples/index.md).
- Изучите статью о [структуре определения Политики Azure](../concepts/definition-structure.md).
- Изучите [сведения о действии политик](../concepts/effects.md).
- Узнайте, как [программно создавать политики](programmatically-create.md).
- Узнайте, как [исправлять несоответствующие ресурсы](remediate-resources.md).
- Дополнительные сведения о группе управления см. в статье [Упорядочивание ресурсов с помощью групп управления Azure](../../management-groups/overview.md).