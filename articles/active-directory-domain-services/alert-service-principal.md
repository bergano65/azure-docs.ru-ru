---
title: Устранение основных оповещений службы в службах домена Azure AD Документы Майкрософт
description: Узнайте, как устранить неполадки основных оповещений конфигурации службы для служб доменов active Directory Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257964"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Известные проблемы: основные оповещения службы в службах домена Active Directory

[Директора служб](../active-directory/develop/app-objects-and-service-principals.md) — это приложения, которые платформа Azure использует для управления, обновления и обслуживания домена Azure AD DS. При удалении директора службы влияет функциональность в домене Azure AD DS.

Эта статья поможет вам устранить исправить и разрешить основные предупреждения конфигурации, связанные с обслуживанием.

## <a name="alert-aadds102-service-principal-not-found"></a>Предупреждение AADDS102: Директор службы не найден

### <a name="alert-message"></a>Сообщение оповещения

*Директор службы, необходимый для правильного функционирования служб доменов Azure AD, был удален из каталога Azure AD. Эта конфигурация влияет на способность корпорации Майкрософт контролировать, управлять, исправлять и синхронизировать управляемый домен.*

При удалении требуемого директора службы платформа Azure не может выполнять задачи автоматизированного управления. Управляемый домен Azure AD DS может неправильно применять обновления или принимать резервные отпор.

### <a name="check-for-missing-service-principals"></a>Проверка отсутствующих субъектов-служб

Чтобы проверить, какой директор службы отсутствует и нуждается в повторном создании, выполните следующие шаги:

1. На портале Azure выберите **Active Directory Azure** из меню слева навигации.
1. Выберите **Корпоративные приложения**. Выберите *все приложения* из меню типа **приложения,** а затем выберите **Apply**.
1. Поиск каждого из идов приложений. Если существующее приложение не найдено, выполните действия *По* устранению решений, чтобы создать основной службы или перерегистрировать пространство имен.

    | Идентификатор приложения | Решение |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Воссоздание отсутствующих услуг основной](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Перерегистрация в пространстве имен Microsoft.AAD](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Перерегистрация в пространстве имен Microsoft.AAD](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Перерегистрация в пространстве имен Microsoft.AAD](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Воссоздание недостающих директоров службы

Если ID приложения *2565bd9d-da50-47d4-8b85-4c97f6669dc36* отсутствует в каталоге Azure AD, используйте Azure AD PowerShell для выполнения следующих шагов. Для получения дополнительной [install Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)информации см.

1. Установите модуль Azure AD PowerShell и импортируйте его следующим образом:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Теперь воссоздайте основную службу с помощью [cmdlet New-AzureAdServicePrincipal:][New-AzureAdServicePrincipal]

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

Здоровье домена Azure AD DS автоматически обновляется в течение двух часов и удаляет оповещение.

### <a name="re-register-the-microsoft-aad-namespace"></a>Перерегистрация в пространстве имен Microsoft AAD

Если применение ID *443155a6-77f3-45e3-882b-22b3a8d431fb,* *abba844e-bc0e-44b0-947a -dc74e5d09022*, или *d87dcbc6-a371-462e-88e3-28ad15ec4e64* отсутствует в вашем каталоге Azure AD, завершить следующие шаги по перерегистрации поставщика ресурсов *Microsoft.AAD:*

1. На портале Azure ищите **подписки.**
1. Выберите подписку, связанную с доменом Azure AD DS.
1. Из левой навигации выберите **Поставщиков ресурсов.**
1. Поиск *по Microsoft.AAD*, а затем выберите **перерегистрации**.

Здоровье домена Azure AD DS автоматически обновляется в течение двух часов и удаляет оповещение.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Предупреждение AADDS105: приложение синхронизации пароля устарело

### <a name="alert-message"></a>Сообщение оповещения

*Директор службы с идентификатором приложения "d87dcbc6-a371-462e-88e3-28ad15ec4e64" был удален, а затем воссоздан. Отдых оставляет несовместимые разрешения на ресурсах доменных служб Azure AD, необходимых для обслуживания управляемого домена. Синхронизация паролей на управляемом домене может быть затронута.*

Azure AD DS автоматически синхронизирует учетные записи пользователей и учетные данные из Azure AD. При наличии проблемы с приложением Azure AD, используемым для этого процесса, синхронизация учетных данных между DD Azure AD и Azure AD завершается неудачей.

### <a name="resolution"></a>Решение

Чтобы воссоздать приложение Azure AD, используемое для синхронизации учетных данных, используйте Azure AD PowerShell для выполнения следующих шагов. Для получения дополнительной [install Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)информации см.

1. Установите модуль Azure AD PowerShell и импортируйте его следующим образом:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Теперь удалите старое приложение и объект, используя следующие cmdlets PowerShell:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```

После удаления обоих приложений платформа Azure автоматически воссоздает их и пытается возобновить синхронизацию паролей. Здоровье домена Azure AD DS автоматически обновляется в течение двух часов и удаляет оповещение.

## <a name="next-steps"></a>Дальнейшие действия

Если у вас все еще есть проблемы, [откройте запрос службы поддержки Azure][azure-support] для дополнительной помощи в устранении неполадок.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
