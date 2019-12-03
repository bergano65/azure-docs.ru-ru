---
title: Группирование управляемых учетных записей служб для доменных служб Azure AD | Документация Майкрософт
description: Узнайте, как создать групповую управляемую учетную запись службы (gMSA) для использования с управляемыми доменами доменных служб Azure Active Directory
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 9dc7e6341f77fc17ae26f34ea029b3eb5414dcbc
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705318"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Создание групповой управляемой учетной записи службы (gMSA) в доменных службах Azure AD

Приложениям и службам часто требуется удостоверение для самостоятельной проверки подлинности с другими ресурсами. Например, веб-службе может потребоваться пройти проверку подлинности в службе базы данных. Если приложение или служба имеет несколько экземпляров, например ферму веб-серверов, создание и Настройка удостоверений для этих ресурсов вручную занимают много времени.

Вместо этого можно создать групповую управляемую учетную запись службы (gMSA) в управляемом домене доменных служб Azure Active Directory (Azure AD DS). ОС Windows автоматически управляет учетными данными для gMSA, что упрощает управление большими группами ресурсов.

В этой статье показано, как создать gMSA в управляемом домене Azure AD DS с помощью Azure PowerShell.

## <a name="before-you-begin"></a>Перед началом работы

Для работы с этой статьей необходимы следующие ресурсы и привилегии:

* Активная подписка Azure.
    * Если у вас еще нет подписки Azure, создайте [учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте AAD.
    * При необходимости выполните инструкции из руководства по [созданию и настройке Azure Active Directory экземпляра доменных служб][create-azure-ad-ds-instance].
* Виртуальная машина управления Windows Server, присоединенная к управляемому домену AD DS Azure.
    * При необходимости выполните инструкции из руководства по [созданию виртуальной машины управления][tutorial-create-management-vm].

## <a name="managed-service-accounts-overview"></a>Общие сведения об управляемых учетных записях служб

Автономная управляемая учетная запись службы (sMSA) — это учетная запись домена, пароль которой управляется автоматически. Такой подход упрощает управление именами участников-служб и позволяет делегированное управление другим администраторам. Вам не нужно вручную создавать и переворачивать учетные данные для учетной записи.

Групповая управляемая учетная запись службы (gMSA) обеспечивает те же упрощения управления, но для нескольких серверов в домене. GMSA позволяет всем экземплярам службы, размещенным в ферме серверов, использовать один и тот же субъект-службу для работы протоколов взаимной проверки подлинности. Если в качестве субъекта-службы используется gMSA, операционная система Windows снова управляет паролем учетной записи вместо того, чтобы полагаться на администратора.

Дополнительные сведения см. в разделе [групповой управляемый обзор учетных записей служб (gMSA)][gmsa-overview].

## <a name="using-service-accounts-in-azure-ad-ds"></a>Использование учетных записей служб в Azure AD DS

Так как управляемые домены Azure AD DS заблокированы и управляются корпорацией Майкрософт, при использовании учетных записей служб необходимо учитывать следующее.

* Создание учетных записей служб в пользовательских подразделениях (OU) в управляемом домене.
    * Нельзя создать учетную запись службы во встроенных подразделениях " *Пользователи AADDC* " или " *AADDC Computers* ".
    * Вместо этого [Создайте настраиваемое подразделение][create-custom-ou] в управляемом домене Azure AD DS, а затем создайте учетные записи служб в этом настраиваемом подразделении.
* Корневой ключ служб распространения ключей (KDS) предварительно создан.
    * Корневой ключ KDS используется для создания и получения паролей для Gmsa. В AD DS Azure для вас создается корневой каталог KDS.
    * У вас нет привилегий для создания другого или просмотра корневого ключа KDS по умолчанию.

## <a name="create-a-gmsa"></a>Создание gMSA

Сначала создайте настраиваемое подразделение с помощью командлета [New-адорганизатионалунит][New-AdOrganizationalUnit] . Дополнительные сведения о создании пользовательских подразделений и управлении ими см. [в разделе Пользовательские подразделения в Azure AD DS][create-custom-ou].

> [!TIP]
> Чтобы выполнить эти действия для создания gMSA, [Используйте виртуальную машину управления][tutorial-create-management-vm]. Эта виртуальная машина управления уже должна иметь необходимые командлеты AD PowerShell и подключение к управляемому домену.

В следующем примере создается пользовательское подразделение с именем *миневау* в управляемом домене AD DS Azure с именем *aadds.contoso.com*. Используйте собственное подразделение и управляемое доменное имя:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=contoso,DC=COM"
```

Теперь создайте gMSA с помощью командлета [New-адсервицеаккаунт][New-ADServiceAccount] . Определены следующие примеры параметров:

* Параметр **-Name** имеет значение *вебфармсвк* .
* Параметр **-path** задает настраиваемое подразделение для gMSA, созданного на предыдущем шаге.
* Записи DNS и имена субъектов-служб заданы для *WebFarmSvc.aadds.contoso.com*
* Субъектам в *contoso-Server $* разрешено получать пароль, используя удостоверение.

Укажите собственные имена и доменные имена.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aadds.contoso.com `
    -Path "OU=MYNEWOU,DC=contoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aadds.contoso.com/aadds.contoso.com, `
        http/WebFarmSvc.aadds.contoso.com/contoso, `
        http/WebFarmSvc/aadds.contoso.com, `
        http/WebFarmSvc/contoso `
    -PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

Теперь приложения и службы можно настроить для использования gMSA по мере необходимости.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Gmsa см. в статье [Приступая к работе с групповыми управляемыми учетными записями служб][gmsa-start].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
