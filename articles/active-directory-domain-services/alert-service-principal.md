---
title: Разрешение оповещений субъекта-службы в доменных службах Azure AD | Документация Майкрософт
description: Сведения об устранении неполадок с оповещениями о конфигурации субъекта-службы для Azure Active Directory доменных служб
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: iainfou
ms.openlocfilehash: 175bfe63176b78c5aeafc7147c46dd5ab1110325
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257964"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Известные проблемы: Оповещения субъекта-службы в доменных службах Azure Active Directory

[Субъекты-службы](../active-directory/develop/app-objects-and-service-principals.md) — это приложения, которые Платформа Azure использует для управления, обновления и обслуживания управляемого домена AD DS Azure. Если субъект-служба удалена, затронута функциональность в управляемом домене Azure AD DS.

Эта статья поможет вам устранить неполадки и устранить связанные с субъектами настройки предупреждения.

## <a name="alert-aadds102-service-principal-not-found"></a>Оповещение AADDS102: Субъект-служба не найдена

### <a name="alert-message"></a>Предупреждающее сообщение

*Субъект-служба, необходимая для правильной работы доменных служб Azure AD, удалена из каталога Azure AD. Эта конфигурация влияет на возможность корпорации Майкрософт наблюдать за управляемым доменом, управлять им, обновлять и синхронизировать его.*

При удалении требуемого субъекта-службы Платформа Azure не сможет выполнять автоматизированные задачи управления. Управляемый домен AD DS Azure может неправильно применять обновления или создавать резервные копии.

### <a name="check-for-missing-service-principals"></a>Проверка отсутствующих субъектов-служб

Чтобы проверить, какой субъект-служба отсутствует и необходимо создать заново, выполните следующие действия.

1. В портал Azure выберите **Azure Active Directory** в меню навигации слева.
1. Выберите **Корпоративные приложения**. Выберите *все приложения* в раскрывающемся меню **Тип приложения** , а затем нажмите кнопку **Применить**.
1. Найдите идентификаторы всех приложений. Если существующее приложение не найдено, следуйте инструкциям по *разрешению* , чтобы создать субъект-службу или повторно зарегистрировать пространство имен.

    | ИД приложения | Разрешение |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Повторное создание отсутствующего субъекта-службы](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Повторно зарегистрируйте пространство имен Microsoft. AAD](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Повторно зарегистрируйте пространство имен Microsoft. AAD](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Повторно зарегистрируйте пространство имен Microsoft. AAD](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Повторное создание отсутствующего субъекта-службы

Если в каталоге Azure AD отсутствует идентификатор приложения *2565bd9d-da50-47d4-8b85-4c97f669dc36* , используйте Azure AD PowerShell, чтобы выполнить следующие действия. Дополнительные сведения см. в [статье Установка Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Установите модуль Azure AD PowerShell и импортируйте его следующим образом:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Теперь Воссоздайте субъект-службу с помощью командлета [New AzureAdServicePrincipal][New-AzureAdServicePrincipal] :

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

Работоспособность управляемого домена Azure AD DS автоматически обновляется в течение двух часов и удаляет оповещение.

### <a name="re-register-the-microsoft-aad-namespace"></a>Повторная регистрация пространства имен Microsoft AAD

Если в каталоге Azure AD отсутствует идентификатор приложения *443155a6-77F3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-947a-dc74e5d09022*или *d87dcbc6-a371-462e-88e3-28ad15ec4e64* , выполните следующие действия, чтобы повторно зарегистрируйте поставщик ресурсов *Microsoft. AAD* :

1. В портал Azure найдите и выберите **подписки**.
1. Выберите подписку, связанную с управляемым доменом AD DS Azure.
1. В области навигации слева выберите **поставщики ресурсов**.
1. Выполните поиск *Microsoft. AAD*, а затем выберите **Повторная регистрация**.

Работоспособность управляемого домена Azure AD DS автоматически обновляется в течение двух часов и удаляет оповещение.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Оповещение AADDS105: приложение синхронизации паролей устарело

### <a name="alert-message"></a>Предупреждающее сообщение

*Субъект-служба с идентификатором приложения d87dcbc6-a371-462e-88e3-28ad15ec4e64 был удален и создан снова. Повторное создание позволяет избавиться от несогласованных разрешений для ресурсов доменных служб Azure AD, необходимых для обслуживания управляемого домена. Это может повлиять на синхронизацию паролей для управляемого домена.*

Azure AD DS автоматически синхронизирует учетные записи пользователей и учетные данные из Azure AD. При возникновении проблемы с приложением Azure AD, используемым для этого процесса, Синхронизация учетных данных между Azure AD DS и Azure AD завершается сбоем.

### <a name="resolution"></a>Разрешение

Чтобы повторно создать приложение Azure AD, используемое для синхронизации учетных данных, выполните следующие действия с помощью Azure AD PowerShell. Дополнительные сведения см. в [статье Установка Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Установите модуль Azure AD PowerShell и импортируйте его следующим образом:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Теперь удалите старое приложение и объект с помощью следующих командлетов PowerShell:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```

После удаления обоих приложений Платформа Azure автоматически воссоздаст их и попытается возобновить синхронизацию паролей. Работоспособность управляемого домена Azure AD DS автоматически обновляется в течение двух часов и удаляет оповещение.

## <a name="next-steps"></a>Следующие шаги

Если у вас по-прежнему возникают проблемы, отправьте [запрос в службу поддержки Azure][azure-support] для получения дополнительных сведений об устранении неполадок.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
