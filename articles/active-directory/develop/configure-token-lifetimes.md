---
title: Установка времени существования токенов
titleSuffix: Microsoft identity platform
description: Узнайте, как задать время существования маркеров, выдаваемых платформой Microsoft Identity. Узнайте, как управлять политикой Организации по умолчанию, создавать политику для входа в веб-систему, создавать политику для собственного приложения, вызывающего веб-API, и управлять расширенной политикой.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 2815041f32ebd7c2dae235229d1ca19aad253f7d
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92503627"
---
# <a name="configure-token-lifetime-policies-preview"></a>Настройка политик времени жизни токенов (Предварительная версия)
Указывая и изменяя время жизни маркеров для приложений, субъектов-служб и организации в целом, вы сможете реализовать в Azure AD много новых сценариев.  

> [!IMPORTANT]
> После 30 января 2021 клиенты больше не смогут настроить время существования маркера обновления и сеанса, и Azure AD прекратит учитывать существующую конфигурацию маркера обновления и сеанса в политиках после этой даты. Вы по-прежнему можете настроить время существования маркера доступа после его устаревания.  Дополнительные сведения см. [в статье настраиваемое время существования маркеров на платформе Microsoft Identity](active-directory-configurable-token-lifetimes.md).
> Мы реализовали [возможности управления сеансом проверки подлинности](../conditional-access/howto-conditional-access-session-lifetime.md)   в условном доступе Azure AD. С помощью этой новой функции можно настроить время существования маркера обновления, установив частоту входа.


В этом разделе мы рассмотрим несколько распространенных вариантов использования политик, которые помогут вам правильно применять новые правила для следующих параметров:

* Время существования маркера
* Максимальное неактивное время маркера
* Максимальный возраст маркера

На основе этих примеров можно узнать:

* как управлять политикой организации по умолчанию;
* как создать политику для входа в веб-службы;
* как создать политику для собственного приложения, которое вызывает веб-API;
* как управлять расширенной политикой.

## <a name="prerequisites"></a>Обязательные условия
В следующих примерах мы будем создавать, обновлять, связывать и удалять политики для приложений, субъектов-служб и организации в целом. Если вы еще не знакомы с Azure AD, мы рекомендуем узнать, [как получить клиент Azure AD](quickstart-create-new-tenant.md) , прежде чем продолжить работу с этими примерами.  

Чтобы приступить к работе, сделайте следующее:

1. Скачайте последнюю версию [общедоступной предварительной версии модуля Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
2. Чтобы войти в учетную запись администратора Azure AD, выполните команду `Connect`. Эту команду следует выполнять при каждом запуске сеанса.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Чтобы просмотреть все политики, которые созданы в организации, выполните следующую команду. Эту команду следует выполнять после большинства операций в наших примерах. Также она позволит получить ** ** для политик.

    ```powershell
    Get-AzureADPolicy
    ```

## <a name="manage-an-organizations-default-policy"></a>как управлять политикой организации по умолчанию;
В этом примере создается политика, которая позволяет выполнять вход пользователей реже по всей Организации. Для этого создайте политику времени существования маркера для однофакторных маркеров обновления, которые применяются в Организации. Эта политика будет распространяться на все приложения в организации и на все субъекты-службы, для которых не установлены отдельные политики.

1. Создайте политику времени жизни маркеров.

    1. Задайте для маркера однофакторного обновления значение "до-Отмена". маркер будет действовать неограниченно долго — пока не будет отозван. Создайте следующее определение политики:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Чтобы создать политику, выполните командлет [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Чтобы удалить все пробелы, выполните командлет [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Чтобы просмотреть созданную политику и получить ее **идентификатор объекта**, выполните следующую команду:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Обновите политику.

    Если вы решили, что созданная в этом примере политика для службы недостаточно строгая, и хотите, Чтобы задать срок действия однофакторного маркера обновления через два дня, выполните следующую команду:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

## <a name="create-a-policy-for-web-sign-in"></a>как создать политику для входа в веб-службы;

В этом примере мы создадим политику, в соответствии с которой пользователи должны будут чаще выполнять проверку подлинности в веб-приложении. Эта политика определит время жизни для маркеров доступа и идентификатора, а также максимальный возраст многофакторного маркера сеанса для субъекта-службы веб-приложения.

1. Создайте политику времени жизни маркеров.

    Эта политика для входа в веб-службы определит время жизни для маркеров доступа и идентификатора, а также максимальный возраст однофакторного маркера сеанса (2 часа).

    1. Чтобы создать политику, выполните командлет [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Чтобы просмотреть новую политику и получить идентификатор **ObjectID**политики, выполните командлет [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Назначьте политику для субъекта-службы. Кроме того, необходимо получить идентификатор **ObjectID** субъекта-службы.

    1. Используйте командлет [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) , чтобы просмотреть все субъекты-службы Организации или одного субъекта-службы.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Если у вас есть субъект-служба, выполните командлет [Add азуреадсервицепринЦипалполици](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>как создать политику для собственного приложения, которое вызывает веб-API;
В этом примере мы создадим политику, в соответствии с которой пользователи должны будут реже выполнять проверку подлинности. Она также увеличивает длительность периода, в течение которого пользователи могут оставаться неактивными, прежде чем им понадобится выполнить повторную проверку подлинности. Политика применяется к веб-API каждый раз, когда собственные приложения обращаются к этому интерфейсу как к ресурсу.

1. Создайте политику времени жизни маркеров.

    1. Чтобы создать ограниченную политику для веб-API, выполните командлет [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Чтобы просмотреть новую политику, выполните следующую команду:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Назначьте политику для веб-API. Вам потребуется также получить **идентификатор объекта** приложения. Используйте командлет [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) , чтобы найти **ObjectID**приложения, или используйте [портал Azure](https://portal.azure.com/).

    Получите идентификатор объекта ( **ObjectID** ) приложения и назначьте политику:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

## <a name="manage-an-advanced-policy"></a>как управлять расширенной политикой.
В этом примере вы создадите несколько политик, чтобы узнать, как работает система приоритетов. Вы также узнаете, как управлять несколькими политиками, применяемыми к нескольким объектам.

1. Создайте политику времени жизни маркеров.

    1. Чтобы создать политику Организации по умолчанию, которая задает для однофакторного маркера обновления время жизни 30 дней, выполните командлет [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Чтобы просмотреть новую политику, выполните командлет [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Назначьте политику для субъекта-службы.

    Теперь у нас есть политика, которая применяется ко всей организации. Предположим, мы хотим сохранить эту политику со сроком действия 30 дней для определенного субъекта-службы, но изменить максимальное ограничение стандартной политики для организации, указав значение until-revoked (пока не будет отозван).

    1. Чтобы просмотреть всех субъектов-служб Организации, используйте командлет [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) .

    1. Если у вас есть субъект-служба, выполните командлет [Add азуреадсервицепринЦипалполици](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Задайте значение false для флага `IsOrganizationDefault`:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Создайте новую политику организации по умолчанию:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Теперь ваша исходная политика связана с субъектом-службой, а организации назначена новая политика по умолчанию. Важно помнить, что политики, примененные к субъектам-службам, имеют более высокий приоритет, чем политики по умолчанию для организации.

## <a name="next-steps"></a>Дальнейшие действия
Узнайте о [возможностях управления сеансами проверки подлинности](../conditional-access/howto-conditional-access-session-lifetime.md) в условном доступе Azure AD.