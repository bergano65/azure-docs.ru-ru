---
title: Развертывание настраиваемых политик с помощью Azure Pipelines
titleSuffix: Azure AD B2C
description: Узнайте, как развертывать пользовательские политики Azure AD B2C в конвейере CI/CD с помощью Azure Pipelines в Azure DevOps Services.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 21fde69f404ee535bfe0019a91843297b1752a92
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463145"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Развертывание настраиваемых политик с помощью Azure Pipelines

С помощью конвейера непрерывной интеграции и доставки (CI/CD), настроенного в [Azure pipelines][devops-pipelines], можно включать Azure AD B2C настраиваемые политики в вашу доставку программного обеспечения и автоматизацию управления кодом. При развертывании в разных средах Azure AD B2C, например для разработки, тестирования и производства, рекомендуется удалить ручные процессы и выполнить автоматическое тестирование с помощью Azure Pipelines.

Существует три основных шага, которые необходимо выполнить для включения Azure Pipelines управления пользовательскими политиками в Azure AD B2C.

1. Создание регистрации веб-приложения в клиенте Azure AD B2C
1. Настройка репозитория Azure
1. Настройка конвейера Azure

> [!IMPORTANT]
> Управление Azure AD B2C пользовательскими политиками с помощью конвейера Azure в настоящее время использует операции **предварительной версии** , доступные в конечной точке Microsoft Graph API `/beta`. Использование этих API для приложений в рабочей среде не поддерживается. Дополнительные сведения см. в [справочнике по конечной точке бета-версии Microsoft Graph REST API](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta).

## <a name="prerequisites"></a>Предварительные требования

* [Azure AD B2C клиент](tutorial-create-tenant.md)и учетные данные для пользователя в каталоге с ролью [администратора политики B2C инфраструктура процедур идентификации](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Пользовательские политики](custom-policy-get-started.md) , отправленные в клиент
* [Конвейер Azure](https://azure.microsoft.com/services/devops/pipelines/)и доступ к [Azure DevOps Services проекту][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Поток предоставления учетных данных клиента

Описываемый здесь сценарий использует вызовы между службами между Azure Pipelines и Azure AD B2C с помощью [потока предоставления учетных данных клиента](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)OAuth 2,0. Этот поток предоставления позволяет веб-службе, такой как Azure Pipelines (конфиденциальный клиент), использовать собственные учетные данные вместо олицетворения пользователя для проверки подлинности при вызове другой веб-службы (в данном случае Microsoft Graph API). Azure Pipelines получает маркер в неинтерактивном режиме, а затем выполняет запросы к API Microsoft Graph.

## <a name="register-an-application-for-management-tasks"></a>Регистрация приложения для задач управления

Начните с создания регистрации приложения, которую сценарии PowerShell выполняли Azure Pipelines будут использовать для взаимодействия с Azure AD B2C. Если у вас уже есть регистрация приложения, которая используется для задач автоматизации, можно перейти к разделу [предоставление разрешений](#grant-permissions) .

### <a name="register-application"></a>Регистрация приложения

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-permissions"></a>Предоставление разрешений

Затем предоставьте приложению разрешение на использование API Microsoft Graph для чтения и записи пользовательских политик в клиенте Azure AD B2C.

#### <a name="applications"></a>[Приложения](#tab/applications/)

1. На странице Обзор **зарегистрированного приложения** выберите **Параметры**.
1. В разделе **доступ через API**выберите **необходимые разрешения**.
1. Нажмите кнопку **Добавить**, а затем **выберите API**.
1. Выберите **Microsoft Graph**, а затем **выберите**.
1. В разделе **разрешения приложения**выберите **чтение и запись политик инфраструктуры доверия вашей организации**.
1. Щелкните **выбрать**, затем **Готово**.
1. Щелкните **Предоставить разрешения** и затем выберите **Да**. Для полного распространения разрешений может потребоваться несколько минут.

#### <a name="app-registrations-preview"></a>[Регистрация приложений (предварительная версия)](#tab/app-reg-preview/)

1. Выберите **Регистрация приложений (Предварительная версия)** , а затем выберите веб-приложение, которое должно иметь доступ к Microsoft Graph API. Например, *managementapp1*.
1. В разделе **Управление** выберите **Разрешения API**.
1. В разделе **Настроенные разрешения** выберите **Добавить разрешение**.
1. Перейдите на вкладку **API-интерфейсы Майкрософт** и выберите **Microsoft Graph**.
1. Выберите **Разрешения приложений**.
1. Разверните **политику** и выберите **Policy. ReadWrite. TrustFramework**.
1. Выберите **Добавить разрешения**. В соответствии с инструкциями подождите несколько минут, прежде чем перейти к следующему шагу.
1. Выберите **Предоставить согласие администратора для (имя арендатора)** .
1. Выберите учетную запись администратора, с которой выполнен вход, или выполните вход с учетной записью в арендаторе Azure AD B2C, которой назначена по крайней мере роль *Администратор облачных приложений*.
1. Нажмите кнопку **Принять**.
1. Выберите **Обновить**и убедитесь, что "предоставлено..." отображается в разделе **состояние**. Распространение разрешений может занять несколько минут.

* * *

### <a name="create-client-secret"></a>Создать секрет клиента

Для проверки подлинности с помощью Azure AD B2C сценарий PowerShell должен указать секрет клиента, создаваемый для приложения.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-an-azure-repo"></a>Настройка репозитория Azure

После регистрации приложения управления вы можете настроить репозиторий для файлов политики.

1. Войдите в организацию Azure DevOps Services.
1. [Создайте новый проект][devops-create-project] или выберите существующий проект.
1. В проекте перейдите по адресу **репозиториев** и выберите страницу **файлы** . Выберите существующий репозиторий или создайте его для этого упражнения.
1. Создайте папку с именем *B2CAssets*. Назовите требуемый файл заполнителя *readme.md* и **зафиксируйте** файл. При необходимости этот файл можно удалить позже.
1. Добавьте файлы политики Azure AD B2C в папку *B2CAssets* . К ним относятся *TrustFrameworkBase. XML*, *TrustFrameWorkExtensions. XML*, *SignUpOrSignin. XML*, *ProfileEdit. XML*, *PasswordReset. XML*и другие созданные политики. Запишите имя каждого файла политики Azure AD B2C для использования на более позднем шаге (они используются в качестве аргументов сценария PowerShell).
1. Создайте папку с именем *Scripts* в корневом каталоге репозитория, назовите файл-заполнитель *DeployToB2c. ps1*. Не зафиксируйте файл на этом этапе, вы сделаете это на следующем шаге.
1. Вставьте следующий скрипт PowerShell в *DeployToB2c. ps1*, а затем **зафиксируйте** файл. Сценарий получает маркер из Azure AD и вызывает API Microsoft Graph, чтобы передать политики в папку *B2CAssets* в клиент Azure AD B2C.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Настройка конвейера Azure

После инициализации репозитория и заполнения его файлами настраиваемой политики можно приступать к настройке конвейера выпуска.

### <a name="create-pipeline"></a>Создание конвейера

1. Войдите в организацию Azure DevOps Services и перейдите к своему проекту.
1. В проекте выберите **конвейеры** > **выпуски** > **Новый конвейер**.
1. В разделе **Выбор шаблона**выберите **пустое задание**.
1. Введите **имя этапа**, например *деплойкустомполиЦиес*, а затем закройте панель.
1. Выберите **Добавить артефакт**и в разделе **тип источника**выберите **репозиторий Azure**.
    1. Выберите исходный репозиторий, содержащий папку *Scripts* , заполненную сценарием PowerShell.
    1. Выберите **ветвь по умолчанию**. Если вы создали новый репозиторий в предыдущем разделе, ветвь по умолчанию — *master*.
    1. Оставьте параметр **версии по умолчанию** *Последняя из ветви по умолчанию*.
    1. Введите **Псевдоним источника** для репозитория. Например, *полицирепо*. Не включайте пробелы в имя псевдонима.
1. Выберите **Добавить**.
1. Переименуйте конвейер, чтобы он отражал свое намерение. Например, *разверните пользовательский конвейер политики*.
1. Нажмите кнопку **сохранить** , чтобы сохранить конфигурацию конвейера.

### <a name="configure-pipeline-variables"></a>Настройка переменных конвейера

1. Перейдите на вкладку **переменные** .
1. Добавьте следующие переменные в **переменные конвейера** и задайте их значения, как указано ниже.

    | Имя | Значение |
    | ---- | ----- |
    | `clientId` | **Идентификатор приложения (клиента)** приложения, зарегистрированного ранее. |
    | `clientSecret` | Значение **секрета клиента** , созданного ранее. <br /> Измените тип переменной на **секрет** (щелкните значок замка). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, где *B2C* — имя клиента Azure AD B2C. |

1. Нажмите кнопку **сохранить** , чтобы сохранить переменные.

### <a name="add-pipeline-tasks"></a>Добавление задач конвейера

Затем добавьте задачу для развертывания файла политики.

1. Перейдите на вкладку **задачи** .
1. Выберите **Задание агента**, а затем щелкните знак "плюс" ( **+** ), чтобы добавить задачу в задание агента.
1. Найдите и выберите **PowerShell**. Не выбирайте "Azure PowerShell," PowerShell на целевых компьютерах "или другую запись PowerShell.
1. Выберите только что добавленную задачу « **скрипт PowerShell** ».
1. Введите следующие значения для задачи "скрипт PowerShell":
    * **Версия задачи**: 2. *
    * **Отображаемое имя**: имя политики, которую должна передать эта задача. Например, *B2C_1A_TrustFrameworkBase*.
    * **Тип**: путь к файлу
    * **Путь к сценарию**: нажмите кнопку с многоточием (***...***), перейдите в папку *Scripts* и выберите файл *DeployToB2C. ps1* .
    * **Даваемых**

        Введите следующие значения **аргументов**. Замените `{alias-name}` псевдонимом, указанным в предыдущем разделе.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Например, если вы указали псевдоним *полицирепо*, строка аргумента должна иметь следующее значение:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/contosob2cpolicies/B2CAssets/TrustFrameworkBase.xml
        ```

1. Нажмите кнопку **сохранить** , чтобы сохранить задание агента.

Задача, которую вы только что добавили, отправляет *один* файл политики в Azure AD B2C. Прежде чем продолжать, вручную активируйте задание (**создать выпуск**), чтобы убедиться, что оно успешно завершается, прежде чем создавать дополнительные задачи.

Если задача завершается успешно, добавьте задачи развертывания, выполнив предыдущие шаги для каждого файла настраиваемой политики. Измените значения аргументов `-PolicyId` и `-PathToFile` для каждой политики.

`PolicyId` — это значение, найденное в начале файла политики XML в узле TrustFrameworkPolicy. Например, `PolicyId` в следующем XML-файле политики *B2C_1A_TrustFrameworkBase*:

```XML
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

При запуске агентов и отправке файлов политики убедитесь, что они передаются в следующем порядке:

1. *TrustFrameworkBase. XML*
1. *TrustFrameworkExtensions. XML*
1. *SignUpOrSignin. XML*
1. *ProfileEdit. XML*
1. *PasswordReset. XML*

Инфраструктура процедур идентификации применяет этот порядок, так как структура файла построена в иерархической цепочке.

## <a name="test-your-pipeline"></a>Тестирование конвейера

Чтобы протестировать конвейер выпуска, выполните следующие действия.

1. Выберите **конвейеры** , а затем **выпуски**.
1. Выберите созданный ранее конвейер, например *деплойкустомполиЦиес*.
1. Выберите **создать выпуск**, а затем выберите **создать** , чтобы поставить в очередь выпуск.

Вы увидите баннер с уведомлением о том, что выпуск поставлен в очередь. Чтобы просмотреть его состояние, выберите ссылку в баннере уведомления или выберите ее в списке на вкладке **выпуски** .

## <a name="next-steps"></a>Следующие шаги

См. также:

* [Вызовы между службами с использованием учетных данных клиента](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines