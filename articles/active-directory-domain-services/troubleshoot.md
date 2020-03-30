---
title: Устранение неполадок службы функциональности Active Directory Domain Services (ru) Документы Microsoft Docs
description: Узнайте, как устранить распространенные ошибки при создании или управлении службами функционального каталога Azure Active Directory Domain
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: e17112cbe2a494a585cd5a09c36cfe449d3d433c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365821"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Общие ошибки и устранение неполадок для служб домена Active Directory

В качестве центральной части идентификации и аутентификации приложений службы azure Active Directory Domain Domain Services (Azure AD DS) иногда возникает проблемы. Если вы столкнулись с проблемами, есть некоторые распространенные сообщения об ошибках и связанные с ними шаги по устранению неполадок, которые помогут вам снова запустить работу. В любое время вы также можете [открыть запрос службы поддержки Azure][azure-support] на дополнительную помощь в устранении неполадок.

В этой статье приводятся шаги по устранению неполадок для общих проблем в Azure AD DS.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Не удается включить доменные службы Azure AD для каталога Azure AD

Если у вас возникли проблемы с включением Azure AD DS, просмотрите следующие распространенные ошибки и шаги для их устранения:

| **Сообщение об ошибке образца** | **Решение** |
| --- |:--- |
| *Имя aaddscontoso.com уже используется в этой сети. Укажите имя, которое не используется.* |[Конфликт доменных имен в виртуальной сети](troubleshoot.md#domain-name-conflict) |
| *Службы домена не могут быть включены в этом арендаторе Azure AD. Служба не имеет адекватных разрешений на приложение под названием "Azure AD Domain Services Sync". Удалите приложение под названием «Azure AD Domain Services Sync», а затем попытайтесь включить службы домена для вашего клиента Azure AD.* |[Службы домена не имеют адекватных разрешений на приложение Sync-сервисов Azure AD](troubleshoot.md#inadequate-permissions) |
| *Службы домена не могут быть включены в этом арендаторе Azure AD. Приложение Domain Services в вашем арендаторе Azure AD не имеет необходимых разрешений для включения domain Services. Удалите приложение с помощью идентификатора приложения d87dcbc6-a371-462e-88e3-28ad15ec4e64, а затем попытайтесь включить domain Services для вашего арендатора Azure AD.* |[Приложение Domain Services не настроено должным образом в вашем атакжеме AD](troubleshoot.md#invalid-configuration) |
| *Службы домена не могут быть включены в этом арендаторе Azure AD. Приложение Microsoft Azure AD отключено в вашем атте-арендаторе Azure AD. Включите приложение с идентификатором приложения 00000002-0000-c000-000000000000000000000000, а затем попытаться включить domain Services для вашего клиента Azure AD.* |[Приложение Microsoft Graph отключено в клиенте Azure AD](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Конфликт доменных имен

**Сообщение об ошибке**

*Имя aaddscontoso.com уже используется в этой сети. Укажите имя, которое не используется.*

**Решение**

Убедитесь, что в той же виртуальной сети нет существующей среды AD DS с тем же доменным именем. Например, у вас может быть домен DS AD dS под названием *aaddscontoso.com,* который работает на M Azure VMs. При попытке включить управляемый домен Azure AD DS с тем же доменным именем *aaddscontoso.com* в виртуальной сети, запрашиваемая операция завершается сбой.

Этот сбой из-за названия конфликтов для доменного имени в виртуальной сети. Поиск DNS проверяет, отвечает ли существующая среда AD DS на запрашиваемое доменное имя. Чтобы устранить этот сбой, используйте другое имя для настройки домена Azure AD DS или де-предоставить существующий домен AD DS, а затем повторите попытку включить Azure AD DS.

### <a name="inadequate-permissions"></a>Недостаточно разрешений

**Сообщение об ошибке**

*Службы домена не могут быть включены в этом арендаторе Azure AD. Служба не имеет адекватных разрешений на приложение под названием "Azure AD Domain Services Sync". Удалите приложение под названием «Azure AD Domain Services Sync», а затем попытайтесь включить службы домена для вашего клиента Azure AD.*

**Решение**

В каталоге Azure AD-кодов есть приложение под названием *Azure AD Domain Services Sync.* Если это приложение существует, удалите его, а затем повторите попытку включить Azure AD DS. Чтобы проверить существующее приложение и удалить его при необходимости, выполните следующие шаги:

1. На портале Azure выберите **Active Directory Azure** из меню слева навигации.
1. Выберите **Корпоративные приложения**. Выберите *все приложения* из меню типа **приложения,** а затем выберите **Apply**.
1. В поле поиска введите *синхронизацию доменов Azure AD.* Если приложение существует, выберите его и выберите **Удалить**.
1. После удаления приложения попробуйте снова включить DS Azure AD.

### <a name="invalid-configuration"></a>Недопустимая конфигурация

**Сообщение об ошибке**

*Службы домена не могут быть включены в этом арендаторе Azure AD. Приложение Domain Services в вашем арендаторе Azure AD не имеет необходимых разрешений для включения domain Services. Удалите приложение с помощью идентификатора приложения d87dcbc6-a371-462e-88e3-28ad15ec4e64, а затем попытайтесь включить domain Services для вашего арендатора Azure AD.*

**Решение**

Проверьте, есть ли у вас существующее приложение под названием *AzureActiveDirectoryDomainControllerServices* с идентификатором *приложения d87dcbc6-a371-462e-88e3-28ad15ec4e64* в каталоге Azure AD. Если это приложение существует, удалите его, а затем повторите попытку включить Azure AD DS.

Используйте следующий скрипт PowerShell для поиска существующего экземпляра приложения и его удаления при необходимости:

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```

### <a name="microsoft-graph-disabled"></a>Интерфейс Microsoft Graph отключен

**Сообщение об ошибке**

*Службы домена не могут быть включены в этом арендаторе Azure AD. Приложение Microsoft Azure AD отключено в вашем атте-арендаторе Azure AD. Включите приложение с идентификатором приложения 00000002-0000-c000-000000000000000000000000, а затем попытаться включить domain Services для вашего клиента Azure AD.*

**Решение**

Проверьте, отключили ли вы приложение с идентификатором *000000002-0000-c000-0000000000000000000000.* Это приложение Microsoft Azure AD, которое предоставляет доступ к API Graph вашему клиенту Azure AD. Для синхронизации арендатора Azure AD необходимо включило это приложение.

Чтобы проверить состояние этого приложения и включить его в случае необходимости, выполнить следующие шаги:

1. На портале Azure выберите **Active Directory Azure** из меню слева навигации.
1. Выберите **Корпоративные приложения**. Выберите *все приложения* из меню типа **приложения,** а затем выберите **Apply**.
1. В поле поиска введите *0000002-0000-0000-c000-00000000000000000000000*. Выберите приложение, а затем выберите **Свойства**.
1. Если **включено для пользователей для входе в систему** *No,* установите значение *Yes,* затем выберите **Сохранить.**
1. После включения приложения попробуйте снова включить DS Azure AD.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Пользователи не могут войти в домен, находящийся под управлением доменных служб Azure AD

Если один или несколько пользователей в вашем атакжере AD не могут войти в домен Azure AD DS, выполните следующие шаги по устранению неполадок:

* **Формат учетных данных** - Попробуйте использовать формат `dee@aaddscontoso.onmicrosoft.com`UPN для указания учетных данных, таких как. Формат UPN — рекомендуемый способ указания учетных данных в Azure AD DS. Убедитесь, что этот UPN настроен правильно в Azure AD.

    *SAMAccountName* для вашей учетной записи, например *AADDSCONTOSO'driley* может быть автогенерирован, если в вашем арендаторе есть несколько пользователей с одной и той же приставкой UPN или если ваша приставка UPN слишком длинна. Таким образом, формат *SAMAccountName* для вашей учетной записи может отличаться от того, что вы ожидаете или используете в вашем домене.

* **Синхронизация паролей** - Убедитесь, что вы включили синхронизацию паролей только для [пользователей облачных][cloud-only-passwords] технологий или для [гибридных сред с помощью Azure AD Connect.][hybrid-phs]
    * **Гибридные синхронизированные учетные записи:** Если учетные записи пострадавших пользователей синхронизированы с каталогом, проверьте следующие области:
    
      * Вы развернули или обновили [последний рекомендуемый выпуск Azure AD Connect.](https://www.microsoft.com/download/details.aspx?id=47594)
      * Вы настроили Azure AD Connect для [выполнения полной синхронизации.][hybrid-phs]
      * В зависимости от размера каталога в DS Azure AD может потребоваться некоторое время. Убедитесь, что вы ждете достаточно долго, прежде чем пытаться аутентифицировать сядь по отношению к управляемому домену.
      * Если проблема сохраняется после проверки предыдущих шагов, попробуйте перезапустить *службу синхронизации Microsoft Azure AD.* С сервера Azure AD Connect откройте запрос команды и запустите следующие команды:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **Учетные записи только для облаков:** Если затронутая учетная запись пользователя является учетной записью пользователя только для облака, убедитесь, что [пользователь изменил свой пароль после включения Azure AD DS.][cloud-only-passwords] Этот сбросить пароль приводит к тому, что необходимые хэши учетных данных для служб домена Azure AD будут создаваться.

* **Проверка того, что учетная запись пользователя активна:** по умолчанию пять недействительных попыток пароля в течение 2 минут на управляемом домене приводят к блокировке учетной записи пользователя на 30 минут. Пользователь не может войти в систему, пока учетная запись заблокирована. Через 30 минут учетная запись пользователя автоматически разблокируется.
  * Попытки недействительных паролей в домене Azure AD DS не блокируют учетную запись пользователя в Azure AD. Учетная запись пользователя заблокирована только в управляемом домене. Проверьте состояние учетной записи пользователя в *административной консоли Active Directory (ADAC)* с помощью [управления VM,][management-vm]а не в Azure AD.
  * Вы также можете [настроить тонкозернистые политики паролей,][password-policy] чтобы изменить порог блокировки по умолчанию и продолжительность.

* **Внешние учетные записи** - Убедитесь, что затронутая учетная запись пользователя не является внешней учетной записью в агонелате Azure AD. Примеры внешних учетных `dee@live.com` записей включают учетные записи Майкрософт или учетные записи пользователей из внешнего каталога Azure AD. Azure AD DS не хранит учетные данные для внешних учетных записей пользователей, поэтому они не могут войти в управляемый домен.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Имеется одно или несколько оповещений для управляемого домена

При наличии активных оповещений в домене Azure AD DS есть активные оповещения, это может помешать процессу проверки подлинности работать правильно.

Чтобы узнать, есть ли активные оповещения, [проверьте состояние работоспособности домена Azure AD DS.][check-health] Если какие-либо предупреждения показаны, [устранение неполадок и устранение их.][troubleshoot-alerts]

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Пользователи, удаленные из вашего клиента Azure AD, не удаляются из управляемого домена

Azure AD защищает от случайного удаления пользовательских объектов. При удалении учетной записи пользователя из арендатора Azure AD соответствующий объект пользователя перемещается в ячейку для переработки. При синхронизации этой операции удаления с доменом Azure AD DS соответствующая учетная запись пользователя помечается как отключенная. Эта функция поможет восстановить или удалить учетную запись пользователя.

Учетная запись пользователя остается в отключенном состоянии в домене Azure AD DS, даже если вы воссоздаете учетную запись пользователя с тем же UPN в каталоге Azure AD. Чтобы удалить учетную запись пользователя из домена Azure AD DS, необходимо принудительно удалить ее из-за арендатора Azure AD.

Чтобы полностью удалить учетную запись пользователя из домена Azure AD DS, удалите пользователя навсегда из вашего арендатора Azure AD с помощью cmdlet [Remove-MsolUser][Remove-MsolUser] PowerShell с параметром. `-RemoveFromRecycleBin`

## <a name="next-steps"></a>Дальнейшие действия

Если у вас по-прежнему возникает проблемы, [откройте запрос службы поддержки Azure][azure-support] для дополнительной помощи в устранении неполадок.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
