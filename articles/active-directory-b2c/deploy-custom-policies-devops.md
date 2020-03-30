---
title: Развертывание пользовательских политик с помощью трубопроводов Azure
titleSuffix: Azure AD B2C
description: Узнайте, как развернуть пользовательские политики Azure AD B2C в конвейере CI/CD с помощью проводов Azure в службах Azure DevOps.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b23b60ae49a4973fa04e6fa5f795f99536e32e7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188755"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Развертывание пользовательских политик с помощью трубопроводов Azure

Используя конвейер непрерывной интеграции и доставки (CI/CD), настроенный в [Azure Pipelines,][devops-pipelines]можно включить пользовательские политики Azure AD B2C в систему доставки программного обеспечения и автоматизации управления кодом. При развертывании в различных средах Azure AD B2C, например, dev, test и production, мы рекомендуем удалять ручные процессы и выполнять автоматизированное тестирование с помощью azure Pipelines.

Требуется три основных этапа, позволяющих azure Pipelines управлять пользовательскими политиками в Azure AD B2C:

1. Создание регистрации веб-приложений в вашем арендаторе Azure AD B2C
1. Настройка Azure Repo
1. Настройка трубопровода Azure

> [!IMPORTANT]
> Управление пользовательскими политиками Azure AD B2C с помощью Azure Pipeline `/beta` в настоящее время использует операции **предварительного просмотра,** доступные на конечную точку Microsoft GraphPI. Использование этих API для приложений в рабочей среде не поддерживается. Для получения дополнительной [Microsoft Graph REST API beta endpoint reference](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta)информации см.

## <a name="prerequisites"></a>Предварительные требования

* [Арендатор Azure AD B2C](tutorial-create-tenant.md)и учетные данные пользователя в каталоге с ролью [администратора политики B2C IEF](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Пользовательские политики,](custom-policy-get-started.md) загруженные арендатору
* [Приложение управления,](microsoft-graph-get-started.md) зарегистрированное в вашем арендаторе с разрешением Microsoft Graph API *Policy.ReadWrite.TrustFramework*
* [Azure Pipeline](https://azure.microsoft.com/services/devops/pipelines/)и доступ к [проекту службы Azure DevOps][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Поток грантов учетных данных клиентов

Описанный здесь сценарий использует вызовы обслуживания между трубопроводами Azure и Azure AD B2C с помощью [потока предоставления клиентских учетных данных](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)OAuth 2.0. Этот поток грантов позволяет веб-службе, такой как Azure Pipelines (конфиденциальный клиент), использовать свои собственные учетные данные вместо того, чтобы выдавать себя за пользователя для проверки подлинности при вызове другого веб-сервиса (в данном случае API Microsoft Graph). Azure Pipelines получает токен неинтерактивно, а затем делает запросы в Microsoft Graph API.

## <a name="register-an-application-for-management-tasks"></a>Регистрация заявки на выполнение управленческих задач

Как указано в [Prerequisites,](#prerequisites)вам необходима регистрация приложения, которую ваши скрипты PowerShell, выполненные Azure Pipelines, могут использоваться для доступа к ресурсам вашего клиента.

Если у вас уже есть регистрация приложений, которую вы используете для задач автоматизации, убедитесь, что оно получило разрешение **Microsoft Graph** > **Policy.ReadWrite.TrustFramework** в рамках **разрешений API на** регистрацию приложения.**Policy** > 

Для получения инструкций по регистрации приложения управления [см.](microsoft-graph-get-started.md)

## <a name="configure-an-azure-repo"></a>Настройка Azure Repo

С регистрацией приложения управления вы готовы настроить репозиторий для файлов политики.

1. Вопийте в организации Службы обслуживания Azure DevOps.
1. [Создайте новый проект][devops-create-project] или выберите существующий проект.
1. В проекте перейдите к **репозипу** и выберите страницу **Файлов.** Выберите существующий репозиторий или создайте его для этого упражнения.
1. Создайте папку под названием *B2CAssets.* Назовите необходимый файл заполнителя *README.md* и **совершите** файл. Вы можете удалить этот файл позже, если хотите.
1. Добавьте файлы политики Azure AD B2C в папку *B2CAssets.* Это включает в себя *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*, и любые другие политики, которые вы создали. Запись имени файла каждого файла политики Azure AD B2C для использования на более позднем этапе (они используются в качестве аргументов сценария PowerShell).
1. Создайте папку под названием *Скрипты* в корневом каталоге репозитория, назовите файл заполнителя *DeployToB2c.ps1.* Не фиксировать файл в этой точке, вы будете делать это на более позднем этапе.
1. Вставьте следующий сценарий PowerShell в *DeployToB2c.ps1*, затем **совершить** файл. Скрипт приобретает токен из Azure AD и вызывает API Microsoft Graph для загрузки политик в папку *B2CAssets* для вашего клиента Azure AD B2C.

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

С помощью репозитория, инициализированного и заполненного файлами пользовательских политик, вы готовы настроить конвейер выпуска.

### <a name="create-pipeline"></a>Создание конвейера

1. Войдите в организацию служб Azure DevOps и перейдите к проекту.
1. В проекте выберите **Трубопроводы** > **выпускает** > **новый конвейер.**
1. При **выборе шаблона**выберите **пустое задание.**
1. Введите **имя этапа,** например, *DeployCustomPolicies,* затем закройте панель.
1. Выберите **Добавить артефакт,** и под **типом Исхода**— выберите **хранилище Azure.**
    1. Выберите исходное репозиторий, содержащий папку *скриптов,* которую вы заселяют скриптом PowerShell.
    1. Выберите **ветку по умолчанию.** Если вы создали новый репозиторий в предыдущем разделе, ветка по умолчанию *является основной*
    1. Оставьте настройку **версии по умолчанию** *Из ветки по умолчанию.*
    1. Введите **псевдоним Source** для репозитория. Например, *policyRepo*. Не включайте пробелы в псевдониме.
1. Выберите **Добавить**
1. Переименуйте конвейер, чтобы отразить его намерения. Например, *развертывание конвейера пользовательской политики.*
1. Выберите **Сохранить,** чтобы сохранить конфигурацию конвейера.

### <a name="configure-pipeline-variables"></a>Настройка переменных конвейера

1. Выберите вкладку **Переменные.**
1. Добавьте следующие переменные в **переменные Pipeline** и установите их значения в указанные:

    | name | Значение |
    | ---- | ----- |
    | `clientId` | **Идентификатор приложения (клиента)** приложения, который вы зарегистрировали ранее. |
    | `clientSecret` | Значение **секрета клиента,** который вы создали ранее. <br /> Измените тип переменной на **секрет** (выберите значок блокировки). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, где *ваш b2c-арендатор* — это имя вашего арендатора Azure AD B2C. |

1. Выберите **Сохранить,** чтобы сохранить переменные.

### <a name="add-pipeline-tasks"></a>Добавление задач конвейера

Затем добавьте задачу для развертывания файла политики.

1. Выберите вкладку **«Задачи».**
1. Выберите **задание агента,** а затем**+** выберите знак плюс (), чтобы добавить задачу к задания агента.
1. Поиск и выбор **PowerShell**. Не выбирайте "Azure PowerShell", "PowerShell на целевых машинах" или другой входе PowerShell.
1. Выберите недавно добавленную задачу **сценария PowerShell.**
1. Введите следующие значения для задачи PowerShell Script:
    * **Версия задачи**: 2.
    * **Отобразите имя**: Имя политики, которую должна загрузить эта задача. Например, *B2C_1A_TrustFrameworkBase*.
    * **Тип**: Путь файла
    * **Путь скрипта**: Выберите эллипсис ***(...),*** перейдите к папке *Скриптов,* а затем выберите файл *DeployToB2C.ps1.*
    * **Аргументы:**

        Введите следующие значения для **Аргументов**. Замените `{alias-name}` псевдоним, указанный в предыдущем разделе.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Например, если указанный вами псевдоним является *policyRepo,* строка аргумента должна быть:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Выберите **Сохранить,** чтобы сохранить работу агента.

Только что добавленная задача загружает *один* файл политики в Azure AD B2C. Перед началом работы вручную запустите задание **(Создать выпуск),** чтобы убедиться, что оно успешно завершится перед созданием дополнительных задач.

Если задача успешно выполнена, добавьте задачи развертывания, выполнив предыдущие шаги для каждого из файлов пользовательской политики. Изменение `-PolicyId` значений и `-PathToFile` аргументов для каждой политики.

Значение `PolicyId` найдено в начале файла политики XML в узлах TrustFrameworkPolicy. Например, `PolicyId` в следующей политике XML *B2C_1A_TrustFrameworkBase:*

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

При запуске агентов и загрузке файлов политики убедитесь, что они загружены в этом порядке:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

Рамочка опыта идентификации обеспечивает выполнение этого порядка, поскольку структура файла построена на иерархической цепочке.

## <a name="test-your-pipeline"></a>Тестирование конвейера

Для тестирования конвейера выпуска:

1. Выберите **трубопроводы,** а затем **релизы**.
1. Выберите созданный ранее конвейер, *например, DeployCustomPolicies.*
1. Выберите **Создать релиз,** затем выберите **Создать** в очередь релиза.

Вы должны увидеть баннер уведомлений, в который сказано, что выпуск был поставлен в очередь. Чтобы просмотреть его статус, выберите ссылку в баннере уведомлений или выберите ее в списке на вкладке **«Релизы».**

## <a name="next-steps"></a>Дальнейшие действия

См. также:

* [Звонки от службы в службу с использованием учетных данных клиентов](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Услуги Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines
