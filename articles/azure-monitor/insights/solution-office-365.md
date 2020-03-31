---
title: Решение по управлению Office 365 в Azure | Документация Майкрософт
description: В этой статье представлены сведения о настройке и использовании решения Office 365 в Azure.  Она содержит подробное описание записей Office 365, создаваемых в службе Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/08/2019
ms.openlocfilehash: 0018ae55ab74e691577a34a397c15355587e0fac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663270"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Решение по управлению Office 365 в Azure (предварительная версия)

![Логотип Office 365](media/solution-office-365/icon.png)


> [!IMPORTANT]
> ## <a name="solution-update"></a>Обновление решений
> Это решение было заменено решением [Office 365](../../sentinel/connect-office-365.md) General Availability в [Azure Sentinel](../../sentinel/overview.md) и решением для [мониторинга AD Azure AD.](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) Вместе они предоставляют обновленную версию предыдущего решения Azure Monitor Office 365 с улучшенным опытом конфигурации. Вы можете продолжать использовать существующее решение до 30 апреля 2020 года.
> 
> Azure Sentinel — это облачное решение для обеспечения информации и управления событиями, которое глотает журналы и предоставляет дополнительные функциональные возможности SIEM, включая обнаружение, исследования, охоту и машинное обучение. Теперь с помощью Azure Sentinel вам будет использоваться журналы активности Office 365 SharePoint и Exchange Management.
> 
> Отчетность Azure AD предоставляет более полное представление журналов из деятельности Azure AD в вашей среде, включая знак в событиях, события аудита и изменения в каталоге. Для подключения журналов Azure AD можно использовать [разъем Azure Sentinel AD](../../sentinel/connect-azure-active-directory.md) или настроить [интеграцию журналов Azure AD с Azure Monitor.](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 
>
> Коллекция журнала Azure AD подвергается ценам Azure Monitor.  Для получения дополнительной информации [оценивайте цены Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/)
>
> Для использования решения Azure Sentinel Office 365:
> 1. Использование разъема Office 365 в Azure Sentinel влияет на цены рабочего пространства. Для получения дополнительной [Azure Sentinel pricing](https://azure.microsoft.com/pricing/details/azure-sentinel/)информации см.
> 2. Если вы уже используете решение Azure Monitor Office 365, необходимо сначала удалить его с помощью скрипта в [разделе Uninstall ниже.](#uninstall)
> 3. [Включите решение Azure Sentinel](../../sentinel/quickstart-onboard.md) в рабочем пространстве.
> 4. Перейдите на страницу **разъемов данных** в Azure Sentinel и включите разъем **Office 365.**
>
> ## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-april-30th"></a>В: Возможно ли на борту решения Office 365 Azure Monitor в период до 30 апреля?
> Нет, решение Azure Monitor Office 365, наносящее на бортскрипты, больше не доступно. Решение будет удалено 30 апреля.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>В: Будут ли изменены таблицы и схемы?
> Имя таблицы **OfficeActivity** и схема останутся такими же, как и в текущем решении. Вы можете продолжить использование тех же запросов в новом решении, исключая запросы, ссылающиеся на данные Azure AD.
> 
> Новые журналы [отчетов и решений Azure AD](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) будут входить в таблицы [SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) и [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) вместо **OfficeActivity.** Для получения дополнительной информации узнайте, [как анализировать журналы Azure AD,](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md)которые также актуальны для пользователей Azure Sentinel и Azure Monitor.
> 
> Ниже приведены образцы для преобразования запросов от **OfficeActivity** к **SigninLogs:**
> 
> **Запрос не удалось войти в систему, пользователем:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId 
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Просмотр операций Azure AD:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>В: Как я могу на борту Azure Sentinel?
> Azure Sentinel — это решение, которое можно включить в новое или существующее рабочее пространство для анализа журналов. Чтобы узнать больше, смотрите [документацию на посадку на посадку Azure Sentinel.](../../sentinel/quickstart-onboard.md)
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>В: Нужен ли мне Azure Sentinel для подключения журналов Azure AD?
> Можно настроить [интеграцию журналов Azure AD с Azure Monitor,](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)которая не связана с решением Azure Sentinel. Azure Sentinel предоставляет нативный разъем и содержимое коробок для журналов Azure AD. Для получения дополнительной информации смотрите вопрос ниже на нестандартном контенте, ориентированном на безопасность.
>
> ###   <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>В: В чем разница при подключении журналов Azure AD из Azure Sentinel и Azure Monitor?
> Azure Sentinel и Azure Monitor подключаются к журналам Azure AD на основе того же [решения для отчетности и мониторинга Azure AD.](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) Azure Sentinel предоставляет нативную разъем с одним щелчком мыши, который соединяет одни и те же данные и предоставляет информацию о мониторинге.
>
> ###   <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>В: Что нужно изменить при переходе на новые таблицы отчетов и мониторинга Azure AD?
> Все запросы с использованием данных Azure AD, включая запросы в оповещениях, панели мониторинга и любой контент, созданный с помощью данных Office 365 Azure AD, должны быть воссозданы с помощью новых таблиц.
>
> Azure Sentinel и Azure AD предоставляют встроенный контент, который можно использовать при переходе к решению для отчетов и мониторинга Azure AD. Для получения дополнительной информации смотрите следующий вопрос о нестандартном контенте, ориентированном на безопасность, и [как использовать рабочие книги Azure Monitor для отчетов Active Directory Azure.](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>В: Как я могу использовать контент, ориентированный на безопасность Azure Sentinel, вне коробки?
> Azure Sentinel предоставляет готовые панели мониторинга, запросы на заказ, запросы на охоту, запросы на исследования и автоматизированные возможности реагирования на основе журналов Office 365 и Azure AD. Узнайте больше о Azure Sentinel GitHub и учебниках:
>
> - [Обнаружение угроз вне коробки](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Создание настраиваемых правил аналитики для обнаружения потенциальных угроз](../../sentinel/tutorial-detect-threats-custom.md)
> - [Мониторинг данных](../../sentinel/tutorial-monitor-your-data.md)
> - [Расследовать инциденты с Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)
> - [настройке автоматического реагирования на угрозы в Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Сообщество Azure Sentinel GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>В: Предоставляет ли Azure Sentinel дополнительные разъемы в рамках решения?
> Да, см. Доступ К источникам [данных Azure Sentinel.](../../sentinel/connect-data-sources.md)
> 
> ###   <a name="q-what-will-happen-on-april-30-do-i-need-to-offboard-beforehand"></a>В: Что произойдет 30 апреля? Нужно ли мне заранее выходить за борт?
> 
> - Вы не сможете получать данные из решения **Office365.** Решение больше не будет доступно в Marketplace
> - Для клиентов Azure Sentinel решение рабочего пространства **Office365** будет включено в решение Azure Sentinel **SecurityInsights.**
> - Если вы не отключите решение вручную, ваши данные будут автоматически отключены 30 апреля.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>В: Переведут ли мои данные в новое решение?
> Да. При удалении решения **Office 365** из рабочего пространства его данные временно станут недоступными из-за удаления схемы. При входе нового разъема **Office 365** в Sentinel схема будет восстановлена в рабочей области и любые уже собранные данные станут доступными. 
 

Решение по управлению Office 365 позволяет выполнять мониторинг среды Office 365 в Azure Monitor.

- Отслеживать действия пользователей в учетных записях Office 365 для анализа шаблонов использования, а также определять поведенческие тенденции. Например, можно извлечь конкретные сценарии использования, такие как файлы, к которым предоставлен общий доступ за пределами организации, или самые популярные сайты SharePoint.
- Отслеживать действия администратора, чтобы контролировать изменения конфигурации или операции с высоким уровнем привилегий.
- Выявлять и анализировать нежелательное поведение пользователей, которое можно настраивать, исходя из потребностей организации.
- Выполнять аудит и проверку соответствия. Например, можно отслеживать операции доступа к файлам для конфиденциальных файлов, что поможет в процессе аудита и проверки соответствия.
- Оперативно устраняйте неполадки с помощью [запросов журнала](../log-query/log-query-overview.md) на основе данных о действиях Office 365 в вашей организации.


## <a name="uninstall"></a>Удаление

Вы можете удалить решение по управлению Office 365 с помощью процесса, описанного в этом [разделе](solutions.md#remove-a-monitoring-solution). Однако это не остановит сбор данных в Office 365 и последующую их отправку в Azure Monitor. Выполните процедуру ниже, чтобы отменить подписку на Office 365 и прекратить сбор данных.

1. Сохраните следующий сценарий как *office365_unsubscribe.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Выполните скрипт с помощью следующей команды:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Пример

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

Вам будет предложено для учетных данных. Предоставьте учетные данные для рабочего пространства Log Analytics.

## <a name="data-collection"></a>сбор данных

Для сбора данных может потребоваться несколько часов. После запуска сбора при создании каждой записи Office 365 отправляет в службу Azure Monitor [уведомление веб-перехватчика](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) с подробными данными. Эта запись становится доступной в Azure Monitor в течение нескольких минут после ее получения.

## <a name="using-the-solution"></a>Использование решения

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Когда вы добавите решение Office 365 в рабочую область Log Analytics, плитка **Office 365** будет добавлена на вашу панель инструментов. На этой плитке отображается количество и графическое представление количества компьютеров в вашей среде и соответствие обновлений.<br><br>
![Плитка сводки Office 365](media/solution-office-365/tile.png)  

Щелкните элемент **Office 365**, чтобы открыть панель мониторинга **Office 365**.

![Панель мониторинга Office 365](media/solution-office-365/dashboard.png)  

Панель мониторинга содержит столбцы, перечисленные в приведенной ниже таблице. Каждый столбец содержит десять ведущих оповещений с числом, показывающим соответствие оповещения критериям данного столбца, таким как область действия и диапазон времени. Можно выполнить поиск журналов, выводящий весь список, щелкнув элемент "Просмотреть все" внизу столбца или заголовок этого столбца.

| Столбец | Описание |
|:--|:--|
| Операции | Предоставляет сведения об активных пользователях из всех отслеживаемых подписок Office 365. Также можно просмотреть количество действий, выполненных за определенный период времени.
| Exchange | Отображает разбивку по действиям Exchange Server, таким как Add-MailboxPermission (Добавление разрешения для почтового ящика) или Set-Mailbox (Настройка почтового ящика). |
| SharePoint | Отображает основные действия, выполняемые пользователями в отношении документов SharePoint. Если выполнить детализацию из этого элемента, то на странице поиска отобразятся сведения об этих действиях, такие как целевой документ и расположение действия. Например, для события доступа к файлу отобразится сам документ, к которому осуществляется доступ, связанное с ним имя учетной записи и IP-адрес. |
| Azure Active Directory | Включает в себя основные действия пользователей, такие как сброс пароля пользователя и попытка входа в систему. Если выполнить детализацию, то можно просмотреть подробные сведения об этих действиях, такие как состояние результата. Это полезно, в основном, для отслеживания подозрительных действий в Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Записи журнала Azure Monitor

У всех записей, создаваемых решением Office 365 в рабочей области Log Analytics в Azure Monitor, свойство **Type** (Тип) имеет значение **OfficeActivity**.  Свойство **OfficeWorkload** определяет, к какой службе Office 365 относится запись — Exchange, Azure Active Directory, SharePoint или OneDrive.  Свойство **RecordType** указывает тип операции.  Эти свойства будут отличаться для каждого типа операции. Их описание приводится в таблице ниже.

### <a name="common-properties"></a>Общие свойства

Следующие свойства являются общими для всех записей Office 365.

| Свойство | Описание |
|:--- |:--- |
| Тип | *OfficeActivity* |
| ClientIP | IP-адрес устройства, которое использовалось при записи действия в журнал. IP-адрес отображается в формате IPv4- или IPv6-адреса. |
| OfficeWorkload | Служба Office 365, к которой относится запись.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Операция | Имя действия пользователя или администратора.  |
| OrganizationId | Идентификатор GUID для клиента Office 365 организации. Это значение для вашей организации всегда будет одинаковым независимо от службы Office 365, в которой оно встречается. |
| RecordType | Тип выполняемой операции. |
| ResultStatus | Указывает, было ли успешным действие (указанное в свойстве Operation). Возможные значения: Succeeded (Успешно), PartiallySucceeded (Выполнено частично) и Failed (Сбой). Для действий администратора Exchange возможные значения — True (Истина) или False (Ложь). |
| UserId | Имя участника-пользователя (UPN) для пользователя, который выполнил действие, приведшее к регистрации в журнале данной записи. Например, my_name@my_domain_name. Обратите внимание, что сюда также включаются записи для действий, выполняемых системными учетными записями (такими как SHAREPOINT\system или NTAUTHORITY\SYSTEM). | 
| UserKey | Альтернативный идентификатор пользователя, определенного в свойстве UserId.  Например, значение этого свойства может заполняться уникальным идентификатором Passport (PUID) для событий, выполняемых пользователями в SharePoint, OneDrive для бизнеса и Exchange. Это свойство также может указывать то же значение, что и свойство UserID событий, происходящих в других службах, и событий, выполняемых системными учетными записями.|
| UserType | Тип пользователя, выполнившего операцию.<br><br>Административный<br>Приложение<br>DcAdmin<br>Обычный<br>Reserved<br>ServicePrincipal<br>Система |


### <a name="azure-active-directory-base"></a>Основа Azure Active Directory

Следующие свойства являются общими для всех записей Azure Active Directory.

| Свойство | Описание |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Тип события Azure AD. |
| ExtendedProperties | Расширенные свойства события Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Вход в учетную запись Azure Active Directory

Эти записи создаются при попытке пользователя Active Directory войти в систему.

| Свойство | Описание |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | Приложение, которое активирует событие входа в учетную запись, например Office 15. |
| `Client` | Подробные сведения о клиентском устройстве, операционной системе устройства и браузере устройства, который использовался при событии входа в учетную запись. |
| `LoginStatus` | Это свойство получается непосредственно из параметра OrgIdLogon.LoginStatus. Сопоставление различных неудачных попыток входа в систему можно выполнить с помощью алгоритмов оповещения. |
| `UserDomain` | Сведения об идентификации клиента (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Эти записи создаются при внесении изменений или дополнений в объекты Azure Active Directory.

| Свойство | Описание |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | Пользователь, в отношении которого было выполнено действие (указанное в свойстве Operation). |
| Субъект | Пользователь или субъект-служба, которые выполнили действие. |
| ActorContextId | Идентификатор GUID организации, к которой принадлежит субъект. |
| ActorIpAddress | IP-адрес субъекта в формате IPv4- или IPv6-адреса. |
| InterSystemsId | Идентификатор GUID, который отслеживает действия по компонентам в службе Office 365. |
| IntraSystemId |   Идентификатор GUID, который создается Azure Active Directory для отслеживания действия. |
| SupportTicketId | Идентификатор запроса пользователя в службу поддержки для действия в ситуациях "действовать от имени". |
| TargetContextId | Идентификатор GUID организации, к которой принадлежит целевой пользователь. |


### <a name="data-center-security"></a>Безопасность центра обработки данных

Эти записи создаются на основе данных аудита безопасности центра обработки данных.  

| Свойство | Описание |
|:--- |:--- |
| EffectiveOrganization | Имя клиента, в отношении которого выполнялось повышение прав или командлет. |
| ElevationApprovedTime | Метка времени, когда был утвержден запрос на повышение прав. |
| ElevationApprover | Имя представителя корпорации Майкрософт. |
| ElevationDuration | Период, в течение которого действовало повышение прав. |
| ElevationRequestId |  Уникальный идентификатор запроса на повышение прав. |
| ElevationRole | Роль, для которой было запрошено повышение прав. |
| ElevationTime | Время начала повышения прав. |
| Start_Time | Время начала выполнения командлета. |


### <a name="exchange-admin"></a>Администратор Exchange

Эти записи создаются при внесении изменений в конфигурацию Exchange.

| Свойство | Описание |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Указывает, кем выполнялся командлет (пользователем организации, персоналом центра обработки данных корпорации Майкрософт, учетной записью службы центра обработки данных или делегированным администратором). Значение False указывает на то, что командлет выполнялся пользователем вашей организации. Значение True означает, что командлет выполнялся персоналом центра обработки данных, учетной записью службы центра обработки данных или делегированным администратором. |
| ModifiedObjectResolvedName |  Это понятное имя объекта, который был изменен с помощью командлета. Эта запись вносится в журнал только в том случае, если командлет изменяет объект. |
| OrganizationName | Имя клиента. |
| OriginatingServer | Имя сервера, из которого выполнялся командлет. |
| Параметры | Имя и значение для всех параметров, которые использовались с командлетом, указанным в свойстве Operations. |


### <a name="exchange-mailbox"></a>Почтовый ящик Exchange

Эти записи создаются при внесении изменений или дополнений в почтовые ящики Exchange.

| Свойство | Описание |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Сведения о почтовом клиенте, который использовался для выполнения операции, такие как версия браузера, версия Outlook или сведения о мобильном устройстве. |
| Client_IPAddress | IP-адрес устройства, которое использовалось при записи операции в журнал. IP-адрес отображается в формате IPv4- или IPv6-адреса. |
| ClientMachineName | Имя компьютера, на котором размещен клиент Outlook. |
| ClientProcessName | Клиент электронной почты, который использовался для доступа к почтовому ящику. |
| ClientVersion | Версия клиента электронной почты. |
| InternalLogonType | Зарезервировано для внутреннего использования. |
| Logon_Type | Указывает тип пользователя, который осуществил доступ к почтовому ящику и выполнил операцию, записанную в журнал. |
| LogonUserDisplayName |    Понятное имя пользователя, который выполнил операцию. |
| LogonUserSid | ИД безопасности пользователя, который выполнил операцию. |
| MailboxGuid | Идентификатор GUID Exchange почтового ящика, к которому осуществлялся доступ. |
| MailboxOwnerMasterAccountSid | ИД безопасности главной учетной записи для учетной записи владельца почтового ящика. |
| MailboxOwnerSid | ИД безопасности владельца почтового ящика. |
| MailboxOwnerUPN | Адрес электронной почты пользователя, владеющего почтовым ящиком, к которому осуществлялся доступ. |


### <a name="exchange-mailbox-audit"></a>Аудит почтового ящика Exchange

Эти записи создаются при создании записей аудита почтового ящика.

| Свойство | Описание |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Item | Представляет элемент, в отношении которого выполнялась операция. | 
| SendAsUserMailboxGuid | Идентификатор GUID Exchange почтового ящика, к которому осуществлялся доступ для отправки почты с помощью команды "Отправить как". |
| SendAsUserSmtp | SMTP-адрес пользователя, олицетворяемого при отправке. |
| SendonBehalfOfUserMailboxGuid | Идентификатор GUID Exchange почтового ящика, к которому осуществлялся доступ для отправки почты с помощью команды "Отправить от имени". |
| SendOnBehalfOfUserSmtp | SMTP-адрес пользователя, от имени которого отправлялась почта. |


### <a name="exchange-mailbox-audit-group"></a>Группа аудита почтового ящика Exchange

Эти записи создаются при внесении изменений или дополнений в группы Exchange.

| Свойство | Описание |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Сведения о каждом элементе в группе. |
| CrossMailboxOperations | Указывает, задействовано ли в операции более одного почтового ящика. |
| DestMailboxId | Используется только в том случае, если параметр CrossMailboxOperations имеет значение True. Указывает GUID целевого почтового ящика. |
| DestMailboxOwnerMasterAccountSid | Используется только в том случае, если параметр CrossMailboxOperations имеет значение True. Указывает ИД безопасности главной учетной записи владельца целевого почтового ящика. |
| DestMailboxOwnerSid | Используется только в том случае, если параметр CrossMailboxOperations имеет значение True. Указывает ИД безопасности целевого почтового ящика. |
| DestMailboxOwnerUPN | Используется только в том случае, если параметр CrossMailboxOperations имеет значение True. Указывает имя участника-пользователя (UPN) владельца целевого почтового ящика. |
| DestFolder | Папка назначения для операций, таких как перемещение. |
| Папка | Папка, где находится группа элементов. |
| Папки |     Сведения об исходных папках, задействованных в операции. Например, если папки выбираются, а затем удаляются. |


### <a name="sharepoint-base"></a>Основа SharePoint

Эти свойства являются общими для всех записей SharePoint.

| Свойство | Описание |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Указывает, что событие произошло в SharePoint. Возможные значения: SharePoint или ObjectModel. |
| ItemType | Тип объекта, к которому осуществлялся доступ или который был изменен. Подробные сведения о типах объектов см. в таблице ItemType. |
| MachineDomainInfo | Сведения об операциях синхронизации устройств. Эти сведения сообщаются только в том случае, если они присутствуют в запросе. |
| MachineId |   Сведения об операциях синхронизации устройств. Эти сведения сообщаются только в том случае, если они присутствуют в запросе. |
| Site_ | Идентификатор GUID сайта, где расположен файл или папка, к которым осуществляется доступ пользователя. |
| Source_Name | Сущность, которая активировала отслеживаемую операцию. Возможные значения: SharePoint или ObjectModel. |
| UserAgent | Сведения о клиенте или браузере пользователя. Эти сведения предоставляются клиентом или браузером. |


### <a name="sharepoint-schema"></a>Схема SharePoint

Эти записи создаются при внесении изменений в конфигурацию SharePoint.

| Свойство | Описание |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Необязательная строка для пользовательских событий. |
| Event_Data |  Необязательные полезные данные для пользовательских событий. |
| ModifiedProperties | Свойство включено для событий администрирования, таких как добавление пользователя в качестве участника сайта или группы администраторов семейства веб-сайтов. Это свойство включает в себя имя свойства, которое было изменено (например, группа администраторов сайта), новое значение измененного свойства (такое как пользователь, добавленный в качестве администратора сайта) и предыдущее значение измененного объекта. |


### <a name="sharepoint-file-operations"></a>Операции с файлами SharePoint

Эти записи создаются в ответ на операции с файлами в SharePoint.

| Свойство | Описание |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Расширение файла, который копируется или перемещается. Это свойство отображается только для событий FileCopied и FileMoved. |
| DestinationFileName | Имя файла, который копируется или перемещается. Это свойство отображается только для событий FileCopied и FileMoved. |
| DestinationRelativeUrl | URL-адрес папки назначения, в которую копируется или перемещается файл. Сочетание значений параметров SiteURL, DestinationRelativeURL и DestinationFileName совпадает со значением свойства ObjectID, которое является полным именем пути для скопированного файла. Это свойство отображается только для событий FileCopied и FileMoved. |
| SharingType | Тип разрешений на совместное использование, назначенных пользователю, которому был предоставлен доступ к ресурсу. Пользователь идентифицируется с помощью параметра UserSharedWith. |
| Site_Url | URL-адрес сайта, где расположен файл или папка, к которым осуществляется доступ пользователя. |
| SourceFileExtension | Расширение файла, к которому осуществлялся доступ пользователя. Это свойство остается пустым, если объект, к которому осуществлялся доступ, является папкой. |
| SourceFileName |  Имя файла или папки, к которым осуществляется доступ пользователя. |
| SourceRelativeUrl | URL-адрес папки, содержащей файл, к которому осуществляется доступ пользователя. Сочетание значений параметров SiteURL, SourceRelativeURL и SourceFileName совпадает со значением свойства ObjectID. Это свойство является полным именем пути для файла, к которому осуществляется доступ пользователя. |
| UserSharedWith |  Пользователь, которому был предоставлен доступ к ресурсу. |




## <a name="sample-log-queries"></a>Запросы образцов журналов

В следующей таблице приведены примеры запросов журнала для записей обновления, собранных этим решением.

| Запрос | Описание |
| --- | --- |
|Количество всех операций для подписки Office 365 |OfficeActivity &#124; summarize count() by Operation |
|Использование сайтов SharePoint|OfficeActivity &#124;, где OfficeWorkload - "точка обмена" &#124; \| суммировать количество () по сортировке SiteUrl по Count asc|
|Операции доступа к файлам по типу пользователя | OfficeActivity &#124; суммировать подсчет () по UserType |
|Мониторинг внешних действий в отношении Exchange|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения об обновлении данных см. в статье [Анализ данных Log Analytics в Azure Monitor](../log-query/log-query-overview.md).
* [Создайте собственные панели мониторинга](../learn/tutorial-logs-dashboards.md) для отображения избранных поисковых запросов Office 365.
* [Создайте оповещения](../platform/alerts-overview.md), чтобы заранее получать уведомления о важных действиях в Office 365.  
