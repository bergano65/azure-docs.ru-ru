---
title: Учетные записи управляемых группой служб для служб доменов Azure AD Документы Майкрософт
description: Узнайте, как создать учетную запись службы управляемой группой (gMSA) для использования в доменах доменов Active Directory Domain Services, управляемых Azure Active Directory Domain Services
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
ms.openlocfilehash: 58749e4518f6fa73c8641ce38483c101576047aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614080"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Создание учетной записи службы управляемой группой (gMSA) в службах домена Azure AD

Приложениям и службам часто требуется идентификация, чтобы проверить подлинность других ресурсов. Например, веб-службе может потребоваться аутентификатизировать с помощью службы базы данных. Если приложение или служба имеет несколько экземпляров, таких как веб-сервер фермы, вручную создание и настройка идентификаторов для этих ресурсов занимает много времени.

Вместо этого учетная запись управляемой группой службы (gMSA) может быть создана в домене Azure Active Directory Domain Services (Azure AD DS). ОС Windows автоматически управляет учетных данными для gMSA, что упрощает управление большими группами ресурсов.

В этой статье показано, как создать gMSA в домене Azure AD DS, управляемом с помощью Azure PowerShell.

## <a name="before-you-begin"></a>Перед началом

Для завершения этой статьи необходимы следующие ресурсы и привилегии:

* Активная подписка Azure.
    * Если у вас еще нет подписки Azure, создайте [учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * При необходимости заполните учебник для [создания и настройки экземпляра служб доменов Active Directory Службы Azure.][create-azure-ad-ds-instance]
* VM управления Windows Server, который соединен с доменом Azure AD DS.
    * При необходимости, завершить учебник для [создания управления VM][tutorial-create-management-vm].

## <a name="managed-service-accounts-overview"></a>Обзор управляемых учетных записей служб

Автономная учетная запись управляемого обслуживания (sMSA) — это учетная запись домена, пароль которой автоматически управляется. Такой подход упрощает управление главным именем службы (SPN) и позволяет делегировать управление другим администраторам. Вам не нужно вручную создавать и поворачивать учетные данные для учетной записи.

Учетная запись группы управляемых служб (gMSA) обеспечивает одно и то же упрощение управления, но для нескольких серверов в домене. GMSA позволяет всем экземплярам службы, размещенной на ферме сервера, использовать один и тот же принцип службы для работы взаимных протоколов аутентификации. Когда gMSA используется в качестве основного обслуживания, операционная система Windows снова управляет паролем учетной записи, а не полагается на администратора.

Для получения дополнительной информации смотрите [обзор управляемых сервисных учетных записей группы (gMSA).][gmsa-overview]

## <a name="using-service-accounts-in-azure-ad-ds"></a>Использование учетных записей служб в Azure AD DS

Поскольку домены, управляемые DS Azure AD, блокируются и управляются корпорацией Майкрософт, при использовании учетных записей служб ы использовались некоторые соображения:

* Создавайте учетные записи служб в пользовательских организационных единицах (OU) на управляемом домене.
    * Вы не можете создать учетную запись службы во встроенных *пользователях AADDC* или *AADDC Computers* OUs.
    * Вместо этого [создайте пользовательский OU][create-custom-ou] в домене Azure AD DS, а затем создайте учетные записи служб в этом пользовательском OU.
* Ключевой ключ служб распределения (KDS) предварительно создан.
    * Корневой ключ KDS используется для генерации и получения паролей для gMSA. В Azure AD DS для вас создается корень KDS.
    * У вас нет привилегий для создания другого или просмотра ключа kDS, ключевого ключа KDS.

## <a name="create-a-gmsa"></a>Создайте групповую управляемую учетную запись службы.

Во-первых, создать пользовательский OU с помощью [New-ADОрганизационныUnit][New-AdOrganizationalUnit] cmdlet. Для получения дополнительной информации о создании [Custom OUs in Azure AD DS][create-custom-ou]и управлении пользовательскими утюми см.

> [!TIP]
> Для выполнения этих шагов для создания gMSA, [используйте управление VM.][tutorial-create-management-vm] Это управление VM уже должны иметь необходимые AD PowerShell cmdlets и подключение к управляемому домену.

Следующий пример создает пользовательский OU под названием *myNewOU* в домене Azure AD DS под названием *aaddscontoso.com.* Используйте свой собственный OU и управляемое доменное имя:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Теперь создайте gMSA с помощью [cmdlet New-ADServiceAccount.][New-ADServiceAccount] Определяются следующие параметры примера:

* **-Имя** устанавливается на *WebFarmSvc*
* **-Параметр Пути** определяет пользовательский OU для gMSA, созданный на предыдущем этапе.
* Записи DNS и основные имена служб ы устанавливаются для *WebFarmSvc.aaddscontoso.com*
* Директорам *AADDSCONTOSO-SERVER$* разрешено получить пароль, пользующий удостоверение личности.

Укажите свои собственные имена и доменные имена.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

Приложения и службы теперь могут быть настроены для использования gMSA по мере необходимости.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации [Getting started with group managed service accounts][gmsa-start]о gMSA см.

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
