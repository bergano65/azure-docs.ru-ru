---
title: Обновление моделей Azure Analysis Services с помощью службы автоматизации Azure | Документация Майкрософт
description: В этой статье описывается, как обновить модель кода для Azure Analysis Services с помощью службы автоматизации Azure.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a79123d57f80474e1871ef68f9a92ea9417089ac
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572357"
---
# <a name="refresh-with-azure-automation"></a>Обновление с помощью службы автоматизации Azure

С помощью службы автоматизации Azure и модулей Runbook PowerShell можно выполнять автоматические операции обновления данных в табличных моделях анализа Azure.  

В примере в этой статье используются [модули PowerShell SQLServer](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Пример модуля Runbook PowerShell, демонстрирующий обновление модели, приведен далее в этой статье.  

## <a name="authentication"></a>Аутентификация

Все вызовы должны пройти проверку подлинности с помощью допустимого маркера Azure Active Directory (OAuth 2).  В примере, приведенном в этой статье, для проверки подлинности в Azure Analysis Services используется субъект-служба (SPN).

Дополнительные сведения о создании субъекта-службы см. в разделе [Создание субъекта-службы с помощью портал Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="prerequisites"></a>Предварительные требования

> [!IMPORTANT]
> В следующем примере предполагается, что Azure Analysis Services брандмауэр отключен. Если брандмауэр включен, общедоступный IP-адрес инициатора запроса необходимо будет список разрешений в брандмауэре.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Установка модулей SqlServer из коллекции PowerShell.

1. В учетной записи службы автоматизации Azure щелкните **модули**, а затем — **Обзор коллекции**.

2. В строке поиска выполните поиск **SQLServer**.

    ![Поиск модулей](./media/analysis-services-refresh-azure-automation/1.png)

3. Выберите SqlServer, а затем нажмите кнопку **Импорт**.
 
    ![Импорт модуля](./media/analysis-services-refresh-azure-automation/2.png)

4. Нажмите кнопку **ОК**.
 
### <a name="create-a-service-principal-spn"></a>Создание субъекта-службы (SPN)

Дополнительные сведения о создании субъекта-службы см. в разделе [Создание субъекта-службы с помощью портал Azure](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Настройка разрешений в Azure Analysis Services
 
Создаваемый субъект-служба должен иметь разрешения администратора сервера на сервере. Дополнительные сведения см. в статье [Добавление субъекта-службы к роли администратора сервера](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Разработка модуля Runbook службы автоматизации Azure

1. В учетной записи службы автоматизации создайте ресурс **учетных данных** , который будет использоваться для безопасного хранения субъекта-службы.

    ![Создание учетных данных](./media/analysis-services-refresh-azure-automation/6.png)

2. Введите сведения для учетных данных.  В качестве **имени пользователя введите имя** **участника-службы (SPN**), а в поле **пароль**введите **секрет имени субъекта-службы**.

    ![Создание учетных данных](./media/analysis-services-refresh-azure-automation/7.png)

3. Импорт модуля Runbook службы автоматизации

    ![Импортировать модуль Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Найдите файл **РЕФРЕШ-модел. ps1** , укажите **имя** и **Описание**, а затем нажмите кнопку **создать**.

    ![Импортировать модуль Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. Когда модуль Runbook создан, он автоматически переходит в режим редактирования.  Нажмите кнопку **Опубликовать**.

    ![Публикация модуля Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > Ресурс учетных данных, созданный ранее, извлекается модулем Runbook с помощью команды **Get-AutomationPSCredential** .  Затем эта команда передается в команду PowerShell **Invoke-процессасадатабасе** , чтобы выполнить проверку подлинности для Azure Analysis Services.

6. Протестируйте модуль Runbook, нажав кнопку **Пуск**.

    ![Запуск модуля Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. Заполните параметры **DATABASENAME**, **аналисиссервер**и **REFRESHTYPE** , а затем нажмите кнопку **ОК**. Параметр **WEBHOOKDATA** не требуется при запуске модуля Runbook вручную.

    ![Запуск модуля Runbook](./media/analysis-services-refresh-azure-automation/12.png)

Если модуль Runbook успешно выполнен, вы получите выходные данные следующего вида:

![Успешный запуск](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Использование автономного модуля Runbook службы автоматизации Azure

Модуль Runbook можно настроить для запуска обновления Azure Analysis Servicesной модели по расписанию.

Это можно настроить следующим образом.

1. В модуле Runbook службы автоматизации щелкните **расписания**, а затем **добавьте расписание**.
 
    ![Создать расписание](./media/analysis-services-refresh-azure-automation/14.png)

2. Щелкните **schedule** > **создать новое расписание**, а затем введите сведения.

    ![Настройка расписания](./media/analysis-services-refresh-azure-automation/15.png)

3. Щелкните **Создать**.

4. Заполните параметры расписания. Они будут использоваться при каждом срабатывании модуля Runbook. При выполнении по расписанию параметр **WEBHOOKDATA** должен оставаться пустым.

    ![Настройка параметров](./media/analysis-services-refresh-azure-automation/16.png)

5. Нажмите кнопку **ОК**.

## <a name="consume-with-data-factory"></a>Использование с фабрикой данных

Чтобы использовать модуль Runbook с помощью фабрики данных Azure, сначала создайте **веб-перехватчик** для модуля Runbook. Веб- **перехватчик** предоставит URL-адрес, который можно вызвать с помощью Web Activity фабрики данных Azure.

> [!IMPORTANT]
> Чтобы создать **веб-перехватчик**, необходимо **опубликовать**состояние модуля Runbook.

1. В модуле Runbook службы автоматизации щелкните **веб-перехватчики**, а затем щелкните **Добавить веб-перехватчик**.

   ![Добавить веб-перехватчик](./media/analysis-services-refresh-azure-automation/17.png)

2. Укажите имя и срок действия веб-перехватчика.  Это имя определяет только веб-перехватчик внутри модуля Runbook службы автоматизации, он не формирует часть URL.

   >[!CAUTION]
   >Перед закрытием мастера убедитесь, что вы скопировали URL-адрес, так как его нельзя вернуть после закрытия.
    
   ![Настройка веб-перехватчика](./media/analysis-services-refresh-azure-automation/18.png)

    Параметры веб-перехватчика могут остаться пустыми.  При настройке веб-действия фабрики данных Azure параметры могут передаваться в тело веб-вызова.

3. Настройка **веб-действия** в фабрике данных

### <a name="example"></a>Пример

   ![Пример веб-действия](./media/analysis-services-refresh-azure-automation/19.png)

**URL** -адрес — это URL-адрес, создаваемый веб-перехватчиком.

**Текст** — это документ JSON, который должен содержать следующие свойства:


|Свойство  |Значение  |
|---------|---------|
|**аналисиссервицесдатабасе**     |Имя базы данных Azure Analysis Services <br/> Пример: AdventureWorksDB         |
|**аналисиссервицессервер**     |Имя сервера Azure Analysis Services. <br/> Пример: https: \//westus. asazure. Windows. NET/Servers/MyServer/Models/AdventureWorks/         |
|**датабасерефрештипе**     |Тип выполняемого обновления. <br/> Пример: Full         |

Пример текста JSON:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Эти параметры определяются в сценарии Runbook PowerShell.  При выполнении веб-действия передаются полезные данные JSON WEBHOOKDATA.

Он десериализуется и сохраняется как параметры PowerShell, которые затем используются командой PowerShell Invoke-Процесасдатабасе.

![Десериализованный веб-перехватчик](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Использование гибридной рабочей роли с Azure Analysis Services

Виртуальную машину Azure со статическим общедоступным IP-адресом можно использовать в качестве гибридная рабочая роль службы автоматизации Azure.  Затем этот общедоступный IP-адрес можно добавить в брандмауэр Azure Analysis Services.

> [!IMPORTANT]
> Убедитесь, что общедоступный IP-адрес виртуальной машины настроен как статический.
>
>Дополнительные сведения о настройке гибридных рабочих ролей службы автоматизации Azure см. в статье [Автоматизация ресурсов в центре обработки данных или в облаке с помощью гибридной рабочей роли Runbook](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

После настройки гибридной рабочей роли создайте веб-перехватчик, как описано в разделе [Использование фабрики данных](#consume-with-data-factory).  Единственное отличие заключается в том, что при настройке веб-перехватчика можно выбрать параметр **выполнить в** **гибридной рабочей роли** > .

Пример веб-перехватчика с использованием гибридной рабочей роли:

![Пример веб-перехватчика гибридной рабочей роли](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Пример модуля Runbook PowerShell

В следующем фрагменте кода приведен пример того, как выполнить обновление модели Azure Analysis Services с помощью модуля Runbook PowerShell.

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
[ИНТЕРФЕЙС REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
