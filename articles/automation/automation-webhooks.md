---
title: Запуск Runbook службы автоматизации Azure с помощью объекта webhook
description: Объект Webhook, который позволяет клиенту запустить модуль Runbook в службе автоматизации Azure с помощью HTTP-запроса.  В статье рассматривается создание объекта Webhook и вызов такого объекта для запуска модуля Runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 8cb641f95e7327e80f42df86a56eba8c34e7e598
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367029"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Запуск Runbook службы автоматизации Azure с помощью объекта webhook

Веб-крюк позволяет внешнему сервису запускать определенный запуск в Azure Automation с помощью одного запроса HTTP. Внешние службы включают службы Azure DevOps, журналы GitHub, Azure Monitor и пользовательские приложения. Такая служба может использовать веб-крюк для запуска запуска запуска, не внедряя полное решение с помощью API автоматизации Azure. Вы можете сравнить webhooks с другими методами запуска runbook в [Запуск ежек в Azure автоматизации](automation-starting-a-runbook.md).

> [!NOTE]
> Использование веб-крючка для запуска runbook Python не поддерживается.

![Обзор веб-перехватчиков](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="webhook-properties"></a>Свойства Webhook

В следующей таблице описаны свойства, которые необходимо настроить для объекта Webhook.

| Свойство | Описание |
|:--- |:--- |
| name |Название веб-крючка. Вы можете предоставить любое имя, которое вы хотите, так как оно не подвергается воздействию клиента. Имя используется для идентификации модуля Runbook в службе автоматизации Azure. Рекомендуется задать веб-перехватчику имя, связанное с клиентом, который будет его использовать. |
| URL-адрес |URL-адрес веб-крючка. Это уникальный адрес, который клиент звонит с помощью HTTP POST, чтобы запустить справочник, связанный с веб-хуком. URL-адрес создается автоматически при создании веб-перехватчика. Нельзя указать другой URL-адрес. <br> <br> URL-адрес содержит маркер безопасности, позволяющий сторонней системе вызывать в службу без дополнительной аутентификации. По этой причине следует относиться к URL как к паролю. По соображениям безопасности просматривать URL-адрес можно только на портале Azure при создании веб-крючка. Запишите URL-адрес в надежном месте, чтобы использовать его в дальнейшем. |
| Срок действия | Срок действия веб-крючка, после которого он больше не может быть использован. Можно изменить дату истечения срока действия веб-крючка, если у него не истек срок действия веб-крючка. |
| Активировано | Настройка, указывающая, включен ли веб-крюк по умолчанию при его создании. Если вы установите это свойство для инвалидов, ни один клиент не может использовать webhook. Вы можете установить это свойство при создании веб-крючка или в любое другое время после его создания. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Параметры, используемые при запуске веб-крючка

Веб-крюк может определить значения параметров runbook, которые используются при запуске. Веб-крюк должен включать значения для любых обязательных параметров runbook и может включать значения для факультативных параметров. Значение параметра, настроенное на веб-крюк, может быть изменено даже после создания веб-крючка. Несколько webhooks, связанных с одной runbook может каждый использовать различные значения параметров Runbook. Когда клиент запускает модуль Runbook с помощью веб-перехватчика, клиент не может переопределить значения параметров, определяемые веб-перехватчиком.

Для получения данных от клиента, runbook `WebhookData`поддерживает один параметр под названием . Этот параметр определяет объект, содержащий данные, которые клиент включает в запрос POST.

![Свойства WebhookData](media/automation-webhooks/webhook-data-properties.png)

Параметр `WebhookData` содержит следующие свойства:

| Свойство | Описание |
|:--- |:--- |
| `WebhookName` | Название веб-крючка. |
| `RequestHeader` | Hashtable, содержащий заголовки входящего запроса POST. |
| `RequestBody` | Тело входящего запроса POST. Этот орган сохраняет любой форматирование данных, таких как строка, JSON, XML или закодированная форма. Модуль Runbook должен быть написан для работы с форматом данных, который ожидается. |

Для поддержки `WebhookData` параметра не требуется конфигурация веб-крючка, и запуск не обязан его принимать. Если книга не определяет параметр, любые сведения о запросе, отправленном клиентом, игнорируются.

> [!NOTE]
> При вызове веб-крючка клиент всегда должен хранить любые значения параметров в случае сбоя вызова. При стычке сети или проблеме с подключением приложение не может получить неудавшихся вызовов на веб-крючке.

Если вы указываете `WebhookData` значение для создания webhook, оно переопределяется, когда веб-крюк запускает runbook с данными из запроса POST клиента. Это происходит даже в том случае, если приложение не содержит данных в орган запроса. 

Если вы запустите книгу, которая определяет `WebhookData` с помощью механизма, кроме `WebhookData` webhook, вы можете предоставить значение для этого runbook признает. Это значение должно быть объектом с теми же [свойствами,](#webhook-properties) что и `WebhookData` параметр, так что с ним может работать сранбук так же, как он работает с фактическими `WebhookData` объектами, передаваемыми веб-крюком.

Например, если вы запускаете следующий runbook с портала Azure и хотите передать некоторые образцы данных webhook для тестирования, необходимо передать данные в JSON в пользовательском интерфейсе.

![Параметр WebhookData из пользовательского интерфейса](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Для следующего примера runbook давайте определим следующие свойства для: `WebhookData`

* **WebhookName**: MyWebhook
* **RequestBody**:`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Теперь мы передаем следующий объект JSON в uI для `WebhookData` параметра. Этот пример, с возвратами вагонов и новыми символами, соответствует формату, который передается из веб-крючка.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Запуск параметра WebhookData из пользовательского интерфейса](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation регистрирует значения всех параметров ввода с помощью задания runbook. Таким образом, любой вход, предоставляемый клиентом в запросе Webhook, регистрируется и доступен любому, кто имеет доступ к задания автоматизации. По этой причине необходимо соблюдать осторожность при указании критических данных в вызовах Webhook.

## <a name="webhook-security"></a>Безопасность Webhook

Безопасность веб-крючка зависит от конфиденциальности его URL-адреса, который содержит маркер безопасности, который позволяет вызывать веб-крюк. Azure Automation не выполняет никакой аутентификации по запросу до тех пор, пока он сделан в правильный URL. По этой причине ваши клиенты не должны использовать веб-крючки для runbooks, которые выполняют высокочувствительные операции без использования альтернативных средств проверки запроса.

Можно включить логику в беговую книгу, чтобы определить, вызывается ли она веб-крючком. Попросите `WebhookName` беговую `WebhookData` книжку, проверите свойство параметра. Runbook может выполнять дальнейшую проверку, ища `RequestBody` конкретную информацию в свойствах. `RequestHeader`

Другая стратегия заключается в том, чтобы запустить книгу выполнить некоторую проверку внешнего состояния, когда он получает запрос webhook. Например, рассмотрим рунологию, которая называется GitHub в любое время, когда происходит новый коммит в репозитории GitHub. Запуск может подключиться к GitHub, чтобы проверить, что новый коммит произошел, прежде чем продолжить.

## <a name="creating-a-webhook"></a>Создание объекта Webhook

Чтобы создать новый веб-перехватчик, связанный с модулем Runbook, на портале Azure, воспользуйтесь следующей процедурой.

1. Со страницы Runbooks на портале Azure щелкните в книге, на которую веб-крюк начинает просматривать сведения о runbook. Убедитесь, что поле **состояния** runbook настроено на **опубликовано.**
2. Щелкните **Веб-перехватчик** в верхней части страницы, чтобы открыть страницу Добавить веб-перехватчик.
3. Нажмите **Создать новый веб-крюк,** чтобы открыть страницу Create Webhook.
4. Заполните поля **«Имя** и **дата истечения»** для веб-крючка и укажите, следует ли его включено. Для получения дополнительной информации об этих свойствах можно ознакомиться с [свойствами Webhook.](#webhook-properties)
5. Щелкните значок копирования и нажмите Ctrl+C, чтобы скопировать URL-адрес объекта Webhook. Сохраните URL-адрес в безопасном месте. 

    > [!NOTE]
    > После создания объекта webhook URL-адрес нельзя получить повторно.

   ![URL-адрес Webhook](media/automation-webhooks/copy-webhook-url.png)

1. Щелкните **Параметры**, чтобы указать значения для параметров модуля Runbook. Если в runbook есть обязательные параметры, вы не можете создать веб-крюк, если не предоставите значения.
1. Щелкните **Создать**, чтобы создать объект Webhook.

## <a name="using-a-webhook"></a>Использование объекта Webhook

Чтобы использовать веб-крюк после его создания, `POST` клиент должен выдать запрос HTTP с URL-адресом для webhook. Синтаксис:

```http
http://<Webhook Server>/token?=<Token Value>
```

Клиент получает один из следующих кодов возврата от запроса. `POST`

| Код | Text | Описание |
|:--- |:--- |:--- |
| 202 |Принято |Запрос был принят, и модуль Runbook успешно поставлен в очередь. |
| 400 |Ошибка запроса |Запрос не был принят по одной из следующих причин: <ul> <li>Срок действия объекта webhook истек.</li> <li>Объект webhook отключен.</li> <li>Недопустимый токен в URL-адресе.</li>  </ul> |
| 404 |Не найдено |Запрос не был принят по одной из следующих причин: <ul> <li>Объект webhook не найден.</li> <li>Модуль Runbook не найден.</li> <li>Учетная запись не найдена.</li>  </ul> |
| 500 |Внутренняя ошибка сервера |URL-адрес допустимый, но произошла ошибка. Отправьте запрос повторно. |

Предполагая, что запрос будет успешным, ответ webhook содержит идентификатор работы в формате JSON, как показано ниже. Он содержит один идентификатор вакансий, но формат JSON позволяет потенциальные будущие улучшения.

```json
{"JobIds":["<JobId>"]}
```

Клиент не может определить момент завершения задания Runbook и состояние его завершения из веб-перехватчика. Он может узнать эту информацию, используя идентификатор задания с помощью другого механизма, такого как [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) или [API автоматизации Azure.](/rest/api/automation/job)

## <a name="renewing-a-webhook"></a><a name="renew-webhook"></a>Обновление веб-крючка

При создании веб-крючка срок действия составляет десять лет, после чего он автоматически истекает. После истечения срока действия веб-крючка его не усваивают. Вы можете только удалить, а затем воссоздать его. 

Можно расширить веб-крюк, который не достиг истечения срока действия. Чтобы расширить веб-крюк:

1. Перейдите к runbook, который содержит webhook. 
2. Выберите **Веб-перехватчики** в разделе **Ресурсы**. 
3. Нажмите на веб-крюк, который вы хотите расширить. 
4. На странице Webhook выберите новый срок годности и время и нажмите **Сохранить**.

## <a name="sample-runbook"></a>Пример Runbook

Следующий пример модуля Runbook принимает данные веб-перехватчика и запускает виртуальные машины, указанные в тексте запроса. Чтобы протестировать эту книгу, в вашей учетной записи автоматизации под **Runbooks,** нажмите **Создать runbook**. Если вы не знаете, как создать модуль Runbook, ознакомьтесь с [этой статьей](automation-quickstart-create-runbook.md).

> [!NOTE]
> Для неграфических runbooks PowerShell, `Add-AzAccount` и `Add-AzureRMAccount` псевдонимы для [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Вы можете использовать эти cmdlets или вы можете [обновить свои модули](automation-update-azure-modules.md) в вашей учетной записи автоматизации до последних версий. Возможно, потребуется обновить модули, даже если вы только что создали новую учетную запись Automation.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VMs from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="testing-the-sample"></a>Тестирование примера

В следующем примере модуль Runbook запускается с помощью Webhook из Windows PowerShell. Любой язык, который может сделать запрос HTTP, может использовать веб-крюк. В качестве примера используется Windows PowerShell.

Модуль Runbook ожидает список виртуальных машин, отформатированный в JSON, в теле запроса. Runbook также проверяет, что заголовки содержат определенное сообщение для проверки того, что абонент webhook является действительным.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

В следующем примере показан абонек, доступный `RequestBody` `WebhookData`в справочнике в свойстве . Это значение отформатировано в JSON, чтобы быть совместимым с форматом, включенным в тело запроса.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

На следующем рисунке показан запрос, отправляемый из Windows PowerShell, а также получаемый в результате ответ. Идентификатор задания извлекается из ответа и преобразуется в строку.

![Кнопка Webhook](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать, как использовать Автоматизацию Azure для принятия мер в отношении предупреждений Azure, [см.](automation-create-alert-triggered-runbook.md)
