---
title: Обновление моделей аналитических служб Azure с помощью автоматизации Azure (ru) Документы Майкрософт
description: В этой статье описывается, как кодировать обновления модели для служб анализа Azure с помощью Azure Automation.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a79123d57f80474e1871ef68f9a92ea9417089ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572357"
---
# <a name="refresh-with-azure-automation"></a>Обновление с помощью службы автоматизации Azure

Используя Azure Automation и PowerShell Runbooks, вы можете выполнять автоматизированные операции обновления данных на табулярных моделях Azure Analysis.  

В примере в этой статье используются [модули PowerShell SqlServer.](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps)

Образец PowerShell Runbook, который демонстрирует обновление модели, представлен позже в этой статье.  

## <a name="authentication"></a>Проверка подлинности

Все вызовы должны быть проверены с помощью действительного маркера Active Directory Azure (OAuth 2).  Пример в этой статье будет использоваться для проверки подлинности служб анализа Azure principal (SPN).

Чтобы узнать больше о создании директора службы, [см.](../active-directory/develop/howto-create-service-principal-portal.md)

## <a name="prerequisites"></a>Предварительные требования

> [!IMPORTANT]
> Следующий пример предполагает, что брандмауэр служб анализа Azure отключен. Если брандмауэр включен, то общедоступный IP-адрес инициатора запроса должен быть включен в белый список в брандмауэре.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Установите модули SqlServer из галереи PowerShell.

1. В вашей учетной записи автоматизации Azure, Нажмите **Модули**, а затем **просмотрите галерею**.

2. В панели поиска ищите **SqlServer**.

    ![Модульы поиска](./media/analysis-services-refresh-azure-automation/1.png)

3. Выберите SqlServer, а затем нажмите **Импорт**.
 
    ![Импортный модуль](./media/analysis-services-refresh-azure-automation/2.png)

4. Нажмите кнопку **ОК**.
 
### <a name="create-a-service-principal-spn"></a>Создание директора службы (SPN)

Чтобы узнать о создании директора службы, [см.](../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="configure-permissions-in-azure-analysis-services"></a>Настройка разрешений в службах анализа Azure
 
Директор службы, который вы создаете, должен иметь разрешения администратора сервера на сервере. Дополнительные сведения см. в статье [Добавление субъекта-службы к роли администратора сервера](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Дизайн книги по автоматизации Azure

1. В учетной записи автоматизации создайте ресурс **учетных данных,** который будет использоваться для надежного хранения директора службы.

    ![Создание учетных данных](./media/analysis-services-refresh-azure-automation/6.png)

2. Введите сведения для учетных данных.  Для **имени пользователя,** введите **SPN ClientId,** для **password,** введите **секрет SPN**.

    ![Создание учетных данных](./media/analysis-services-refresh-azure-automation/7.png)

3. Импорт Автоматизация Runbook

    ![Импортировать модуль Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Просмотрите файл **Refresh-Model.ps1,** предоставьте **имя** и **описание,** а затем нажмите **Создать.**

    ![Импортировать модуль Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. Когда Runbook был создан, он автоматически переходит в режим ред.,  Нажмите кнопку **Опубликовать**.

    ![Публикация модуля Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > Созданный ранее ресурс учетных данных извлекается в службу runbook с помощью команды **Get-AutomationPSCredential.**  Затем эта команда передается команде **Invoke-ProcessASADatabase** PowerShell для выполнения аутентификации в службах анализа Azure.

6. Проверьте runbook, нажав **Кнопка**.

    ![Запуск Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. Заполните параметры **DATABASENAME,** **ANALYSISSERVER**и **REFRESHTYPE,** а затем нажмите **OK.** Параметр **WEBHOOKDATA** не требуется, когда Runbook работает вручную.

    ![Запуск Runbook](./media/analysis-services-refresh-azure-automation/12.png)

Если Runbook выполнен успешно, вы получите выход, как следующее:

![Успешный запуск](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Используйте автономный Runbook автоматизации Azure

Runbook можно настроить для запуска обновления модели анализа Azure на плановой основе.

Это может быть настроено следующим образом:

1. В автоматике Runbook нажмите **Расписание,** а затем **добавьте расписание.**
 
    ![Создание расписания](./media/analysis-services-refresh-azure-automation/14.png)

2. Нажмите **Расписание** > **Создайте новое расписание,** а затем заполните детали.

    ![Расписание настройки](./media/analysis-services-refresh-azure-automation/15.png)

3. Нажмите кнопку **Создать**.

4. Заполните параметры для расписания. Они будут использоваться каждый раз, когда runbook триггеров. Параметр **WEBHOOKDATA** должен быть оставлен пустым при запуске по расписанию.

    ![Настройка параметров](./media/analysis-services-refresh-azure-automation/16.png)

5. Нажмите кнопку **ОК**.

## <a name="consume-with-data-factory"></a>Потребление с фабрикой данных

Чтобы использовать книгу runbook с помощью Azure Data Factory, сначала создайте **Webhook** для runbook. **Webhook** предоставит URL-адрес, который можно вызвать через веб-активность Azure Data Factory.

> [!IMPORTANT]
> Для создания **Webhook**статус Runbook должен быть **опубликован.**

1. В вашем автоматике Runbook нажмите **Webhooks,** а затем нажмите **Добавить Webhook**.

   ![Добавить Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Дайте Webhook имя и срок действия.  Название идентифицирует Webhook только внутри автоматики Runbook, он не является частью URL.

   >[!CAUTION]
   >Убедитесь, что вы скопируете URL перед закрытием мастера, так как вы не можете получить его обратно после закрытия.
    
   ![Настройка Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Параметры для веб-крючка могут оставаться пустыми.  При настройке веб-активности Azure Data Factory параметры могут быть переданы в тело веб-вызова.

3. В Data Factory наверсможно настроить **веб-активность**

### <a name="example"></a>Пример

   ![Пример веб-активности](./media/analysis-services-refresh-azure-automation/19.png)

**URL-адрес** — это URL-адрес, созданный из Webhook.

**Тело** представляет собой документ JSON, который должен содержать следующие свойства:


|Свойство  |Значение  |
|---------|---------|
|**AnalysisServicesБаза данных**     |Название базы данных аналитических служб Azure <br/> Пример: AdventureWorksDB         |
|**AnalysisServicesServer**     |Имя сервера аналитических служб Azure. <br/> Пример: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**База данныхRefreshType**     |Тип обновления для выполнения. <br/> Пример: Полный         |

Пример тела JSON:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Эти параметры определены в сценарии Runbook PowerShell.  При выполнении веб-активности пройдена полезная нагрузка JSON webHOOKDATA.

Это десериализовано и хранится в качестве параметров PowerShell, которые затем используются командой Invoke-ProcesASDatabase PowerShell.

![Десериализованный Вебхук](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Используйте гибридный работник с помощью аналитических служб Azure

Виртуальная машина Azure со статичным общедоступным IP-адресом может использоваться в качестве гибридного работника Azure Automation.  Этот общедоступный IP-адрес можно добавить в брандмауэр службанализа Azure Analysis Services.

> [!IMPORTANT]
> Убедитесь, что публичный IP-адрес Virtual Machine настроен как статический.
>
>Чтобы узнать больше о настройке гибридных [Automate resources in your datacenter or cloud by using Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker)работников Azure Automation, см.

После настройки гибридного работника создайте Webhook, как описано в разделе [«Потребляйте с фабрикой данных».](#consume-with-data-factory)  Единственное отличие здесь заключается в том, чтобы выбрать опцию **Run on** > **Hybrid Worker** при настройке Webhook.

Пример webhook с использованием гибридного работника:

![Пример гибридный рабочий Webhook](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Пример PowerShell Runbook

Следующий фрагмент кода является примером того, как выполнять обновление модели анализа Azure С помощью PowerShell Runbook.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Дальнейшие действия

[Примеры](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
