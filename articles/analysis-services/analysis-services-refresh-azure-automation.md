---
title: Обновление моделей Azure Analysis Services со службой автоматизации Azure | Документация Майкрософт
description: Узнайте, как код обновляет модель с использованием службы автоматизации Azure.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 4cae93cff594ad561973f8029ea7335dc4c60263
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357000"
---
# <a name="refresh-with-azure-automation"></a>Обновление с помощью службы автоматизации Azure

С помощью службы автоматизации Azure и модули Runbook PowerShell, можно выполнять автоматические обновления данных в табличных моделях Azure Analysis.  

В примере в этой статье используется [модули PowerShell SqlServer](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Далее в этой статье предоставляется пример модуля PowerShell Runbook, который демонстрирует обновление модели.  

## <a name="authentication"></a>Authentication

Все вызовы должны пройти проверку подлинности допустимый токен Azure Active Directory (OAuth 2).  Пример в этой статье будут выполнять проверку подлинности в Azure Analysis Services (Субъекта-службы).

Дополнительные сведения о создании субъекта-службы, см. в разделе [создать субъект-службу с помощью портала Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="prerequisites"></a>Технические условия

> [!IMPORTANT]
> В следующем примере предполагается, что брандмауэр Azure Analysis Services отключена. Если включен брандмауэр попасть в брандмауэре потребуется общедоступный IP-адрес инициатора запроса.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Установите модули SqlServer из коллекции PowerShell.

1. В учетной записи службы автоматизации Azure, щелкните **модули**, затем **Обзор коллекции**.

2. На панели поиска выполните поиск **SqlServer**.

    ![Поиск модулей](./media/analysis-services-refresh-azure-automation/1.png)

3. Выберите SqlServer, а затем нажмите кнопку **импорта**.
 
    ![Импорт модуля](./media/analysis-services-refresh-azure-automation/2.png)

4. Последовательно выберите **ОК**.
 
### <a name="create-a-service-principal-spn"></a>Создание субъекта-службы (SPN)

Дополнительные сведения о создании субъекта-службы, см. в разделе [создать субъект-службу с помощью портала Azure](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Настройка разрешений в службах Azure Analysis Services
 
Создании субъекта-службы должна иметь разрешения администратора сервера на сервере. Дополнительные сведения см. в статье [Добавление субъекта-службы к роли администратора сервера](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Разработка Runbook службы автоматизации Azure

1. В учетной записи службы автоматизации, создайте **учетные данные** ресурсов, которая будет использоваться для безопасного хранения субъекта-службы.

    ![Создание учетных данных](./media/analysis-services-refresh-azure-automation/6.png)

2. Введите сведения для учетных данных.  Для **имя пользователя**, введите **ClientId имени участника-службы**, для **пароль**, введите **имени участника-службы секрет**.

    ![Создание учетных данных](./media/analysis-services-refresh-azure-automation/7.png)

3. Импорт Runbook службы автоматизации

    ![Импортировать модуль Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Поиск **обновления Model.ps1** файл, укажите **имя** и **описание**, а затем нажмите кнопку **создать**.

    ![Импортировать модуль Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. При создании Runbook автоматически перейдет в режим редактирования.  Нажмите кнопку **Опубликовать**.

    ![Публикация модуля Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > Ресурс учетных данных, который был создан ранее извлекается модулем runbook с помощью **Get-AutomationPSCredential** команды.  Эта команда передается **Invoke ProcessASADatabase** команду PowerShell для проверки подлинности для служб Azure Analysis Services.

6. Тестирование модуля runbook, щелкнув **запустить**.

    ![Запуск модуля Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. Заполните **DATABASENAME**, **строку СЕРВЕР_АНАЛИЗА_ДАННЫХ**, и **REFRESHTYPE** параметров, а затем щелкните **ОК**. **WEBHOOKDATA** параметр не требуется, когда модуль Runbook запускается вручную.

    ![Запуск модуля Runbook](./media/analysis-services-refresh-azure-automation/12.png)

Если модуль Runbook успешно выполнен, вы получите выходные данные следующего вида:

![При успешном выполнении](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Используйте автономное Runbook службы автоматизации Azure

Модуль Runbook можно настроить для триггера, модель Azure Analysis Services обновление по расписанию.

Это можно настроить следующим образом:

1. В модуле Runbook службы автоматизации щелкните **расписания**, затем **Добавить расписание**.
 
    ![Создание расписания](./media/analysis-services-refresh-azure-automation/14.png)

2. Нажмите кнопку **расписание** > **создать новое расписание**, а затем введите необходимые сведения.

    ![Настройка расписания](./media/analysis-services-refresh-azure-automation/15.png)

3. Нажмите кнопку **Создать**.

4. Заполните параметры для расписания. Они будут использоваться каждый раз, активирует модуль Runbook. **WEBHOOKDATA** параметр следует оставить пустым при выполнении по расписанию.

    ![Настройка параметров](./media/analysis-services-refresh-azure-automation/16.png)

5. Последовательно выберите **ОК**.

## <a name="consume-with-data-factory"></a>Использование с фабрикой данных

Чтобы использовать модуль runbook с помощью фабрики данных Azure, необходимо сначала создать **веб-перехватчика** для модуля runbook. **Веб-перехватчика** предоставит URL-адрес, который можно вызывать с помощью веб-действие фабрики данных Azure.

> [!IMPORTANT]
> Чтобы создать **веб-перехватчика**, должна быть в состоянии модуля Runbook **опубликовано**.

1. В модуле Runbook службы автоматизации щелкните **веб-перехватчики**, а затем нажмите кнопку **добавить веб-перехватчик**.

   ![Добавить веб-перехватчик](./media/analysis-services-refresh-azure-automation/17.png)

2. Присвойте веб-перехватчика, имя и срок действия.  Только идентифицируют веб-перехватчика в модуль Runbook службы автоматизации, он не является частью URL-адрес.

   >[!CAUTION]
   >Убедитесь, что скопируйте URL-адрес, прежде чем закрывать вы его не удается вернуть после закрытия мастера.
    
   ![Настройка веб-перехватчика](./media/analysis-services-refresh-azure-automation/18.png)

    Параметры для веб-перехватчик может остаться пустым.  При настройке веб-действие фабрики данных Azure, параметры могут передаваться в текст вызова веб.

3. В фабрике данных, Настройка **веб-действие**

### <a name="example"></a>Пример

   ![Пример веб-действие](./media/analysis-services-refresh-azure-automation/19.png)

**URL-адрес** URL-адрес, созданный из веб-перехватчика.

**Текст** — это документ JSON, которое должно содержать следующие свойства:


|Свойство  |Value  |
|---------|---------|
|**AnalysisServicesDatabase**     |Имя базы данных Azure Analysis Services <br/> Пример: AdventureWorksDB         |
|**AnalysisServicesServer**     |Имя сервера Azure Analysis Services. <br/> Пример: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |Тип обновления для выполнения. <br/> Пример: Полное         |

Пример текста JSON:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Эти параметры определены в модуле runbook сценария PowerShell.  При выполнении веб-действие, переданный полезные данные JSON равно WEBHOOKDATA.

Это десериализуется и хранится как параметры PowerShell, которые затем используются с помощью команды PowerShell Invoke-ProcesASDatabase.

![Десериализованный веб-перехватчика](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Использование гибридной рабочей роли Azure Analysis Services

Виртуальной машине Azure с помощью статический общедоступный IP-адрес может использоваться в качестве гибридной рабочей роли службы автоматизации Azure.  Этот общедоступный IP-адрес, затем добавляются в брандмауэр Azure Analysis Services.

> [!IMPORTANT]
> Убедитесь, что общедоступный IP-адрес виртуальной машины настроен как статический.
>
>Дополнительные сведения о настройке гибридными рабочими ролями службы автоматизации Azure, см. в разделе [автоматизация ресурсов в центре обработки данных или облаке с помощью гибридной рабочей роли Runbook](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

После настройки гибридной рабочей роли, создание объекта Webhook, как описано в разделе [Consume с фабрикой данных](#consume-with-data-factory).  Единственное отличие заключается в том, чтобы выбрать **проведение** > **гибридной рабочей роли** параметр при настройке веб-перехватчика.

Пример веб-перехватчика с помощью гибридной рабочей роли:

![Пример гибридной рабочей роли Webhook](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Пример модуля PowerShell Runbook

В следующем фрагменте кода приведен пример того, как выполнять обновления модели Azure Analysis Services, с помощью PowerShell Runbook.

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
