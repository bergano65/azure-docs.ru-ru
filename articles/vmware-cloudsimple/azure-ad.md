---
title: Решение Azure VMware от CloudSimple - Используйте Azure AD в качестве источника идентификации в частном облаке
description: Описывает, как добавить Azure AD в качестве поставщика идентификационных данных в облачном частном облаке для проверки подлинности пользователей, обращающихся к CloudSimple из Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 674ca8bea110d60557d1e50e7b68c9c3f7a92bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564590"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Используйте Azure AD в качестве поставщика идентификационных данных для vCenter в облачном частном облаке

Вы можете настроить vCenter CloudSimple Private Cloud для проверки подлинности с помощью Active Directory (Azure AD) для администраторов VMware для доступа к vCenter. После настройки единого источника идентификации, пользователь **cloudowner** может добавлять пользователей из источника идентификации в vCenter.  

Вы можете настроить домен Active Directory и контроллеры домена любым из следующих способов:

* Активные контроллеры домена каталога и домена, работающие на месте
* Контроллеры домена Active Directory и домена, работающие на Azure как виртуальные машины в подписке Azure
* Новый домен Active Directory и контроллеры доменов, работающие в облачном частном облаке
* Служба активного каталога Azure

В этом руководстве разъясняются задачи, необходимые для настройки Azure AD в качестве источника идентификации.  Для получения информации об использовании в Azure на местах Active Directory или Active Directory обратитесь в [Set up vCenter, чтобы использовать Active Directory](set-vcenter-identity.md) для получения подробных инструкций по настройке источника идентификации.

## <a name="about-azure-ad"></a>Общие сведения об Azure AD

Azure AD — это служба мультитенантов и управления идентификационными данными Microsoft.  Azure AD предоставляет масштабируемый, последовательный и надежный механизм аутентификации для пользователей для проверки подлинности и доступа к различным службам в Azure.  Он также предоставляет безопасные службы LDAP для любых сторонних служб, чтобы использовать Azure AD в качестве источника аутентификации/идентификации.  Azure AD сочетает в себе основные службы каталога, расширенное управление идентификацией и управление доступом к приложениям, которые могут быть использованы для предоставления доступа к частному облаку для пользователей, управляющих приватным облаком.

Чтобы использовать Azure AD в качестве источника идентификации с vCenter, необходимо настроить службы домена Azure AD и Azure AD. Выполните следующие действия:

1. [Как настроить службы домена Azure AD и Azure AD](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Как настроить источник идентификации в vCenter частного облака](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Настройка служб домена Azure AD и Azure AD

Прежде чем приступить к работе, вам понадобится доступ к подписке Azure с привилегиями Глобального администратора.  Следующие шаги дают общие руководящие принципы. Подробности содержатся в документации Azure.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Если у вас уже есть Azure AD, вы можете пропустить этот раздел.

1. Настройка Azure AD в подписке, описанной в [документации Azure AD.](../active-directory/fundamentals/get-started-azure-ad.md)
2. Включите Azure Active Directory Premium в подписке, описанной в подписке, которая описана в [подписке на Azure Active Directory Premium.](../active-directory/fundamentals/active-directory-get-started-premium.md)
3. Настройте пользовательское доменное имя и проверьте пользовательское доменное имя, описанное в [Добавлении пользовательского доменного имени в Azure Active Directory.](../active-directory/fundamentals/add-custom-domain.md)
    1. Настроите запись DNS в регистраторе домена с информацией, предоставленной на Azure.
    2. Установите пользовательское доменное имя, чтобы быть основным доменом.

Можно дополнительно настроить другие функции Azure AD.  Они не требуются для включения аутентификации vCenter с помощью Azure AD.

### <a name="azure-ad-domain-services"></a>Службы домена Azure AD

> [!NOTE]
> Это важный шаг для включения Azure AD в качестве источника идентификации для vCenter.  Чтобы избежать каких-либо проблем, убедитесь, что все шаги выполняются правильно.

1. Включите доменные службы Azure AD, описанные в [службах домена Enable Azure Active Directory с помощью портала Azure.](../active-directory-domain-services/active-directory-ds-getting-started.md)
2. Навести настройку сети, которая будет использоваться службами домена Azure AD, описанной в [службах домена Enable Azure Active Directory Domain Services с помощью портала Azure.](../active-directory-domain-services/active-directory-ds-getting-started-network.md)
3. Напередить группу администраторов для управления службами домена Azure AD, описанную в [службах домена Active Directory Enable, с помощью портала Azure.](../active-directory-domain-services/active-directory-ds-getting-started-admingroup.md)
4. Обновление параметров DNS для служб домена Azure AD, описанное в [службах домена Enable Azure Active Directory Domain.](../active-directory-domain-services/active-directory-ds-getting-started-dns.md)  Если вы хотите подключиться к AD через Интернет, установите запись DNS для общедоступного IP-адреса доменных служб Azure AD на доменное имя.
5. Включить синхронизацию паролей для пользователей.  Этот шаг позволяет синхронизировать хэши паролей, необходимых для NT LAN Manager (NTLM) и проверки подлинности Kerberos в службах домена Azure AD. Когда синхронизация хэшей паролей настроена, пользователи могут входить в управляемый домен с помощью учетных данных организации. Смотрите [синхронизацию хэша паролей с помощью служб домена Active Directory.](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
    1. При наличии пользователей только для облаков они должны изменить свой пароль с помощью <a href="http://myapps.microsoft.com/" target="_blank">панели доступа Azure AD,</a> чтобы обеспечить хранение хэшов паролей в формате, требуемом NTLM или Kerberos.  Следуйте инструкциям по [синхронизации хэша паролей enable в управляемый домен для учетных записей пользователей только для облачных.](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)  Этот шаг должен быть сделан для отдельных пользователей и любого нового пользователя, который создается в каталоге Azure AD с помощью портала Azure или cmdlets Azure AD PowerShell. Пользователи, которым требуется доступ к службам домена Azure AD, должны использовать <a href="http://myapps.microsoft.com/" target="_blank">панель доступа Azure AD</a> и получить доступ к своему профилю для изменения пароля.

        > [!NOTE]
        > Если у вашей организации есть только учетные записи пользователей в облаке, все пользователи, которые хотят воспользоваться доменными службами Azure Active Directory, должны изменить свои пароли. Облачная учетная запись пользователей — это учетная запись, созданная в каталоге Azure AD с помощью портала Azure или командлетов Azure AD PowerShell. Такие учетные записи пользователей не синхронизированы из локального каталога.

    2. Если вы синхронизируете пароли из каталога Active, выполните последующие действия в [документации Active Directory.](../active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)

6.  Нанастройка защищенного LDAP на ваших службах домена Active Directory, как описано в [Configure secure LDAP (LDAPS) для домена Azure AD Domain.](../active-directory-domain-services/tutorial-configure-ldaps.md)
    1. Загрузите сертификат для использования безопасным LDAP, как описано в теме Azure, [получите сертификат для безопасного LDAP.](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)  CloudSimple рекомендует использовать подписанный сертификат, выданный сертификатом, чтобы убедиться, что vCenter может доверять сертификату.
    2. Включите безопасный LDAP, как описано [Enable Secure LDAP (LDAPS) для домена Azure AD Domain Services, управляемый.](../active-directory-domain-services/tutorial-configure-ldaps.md)
    3. Сохранить общедоступную часть сертификата (без закрытого ключа) в формате .cer для использования с vCenter при настройке источника идентификации.
    4. Если требуется доступ к доменным службам Azure AD, включите опцию «Разрешить безопасный доступ к LDAP через Интернет».
    5. Добавьте правило безопасности входящего для служб домена Azure AD Domain NSG для порта TCP 636.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Настройка источника идентификации в vCenter частного облака

1. [Эскалация привилегий](escalate-private-cloud-privileges.md) для вашего частного облака vCenter.
2. Соберите параметры конфигурации, необходимые для настройки источника идентификации.

    | **Параметр** | **Описание** |
    |------------|-----------------|
    | **Название** | Имя источника идентификации. |
    | **База DN для пользователей** | База выделяемое имя для пользователей.  Для Azure AD `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` используйте: Пример: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`.|
    | **Доменное имя** | Например, example.com. Не предоставляйте IP-адрес в этом текстовом поле. |
    | **Псевдоним домена** | *(необязательно)* Имя домена NetBIOS. Добавьте имя домена Active Directory в качестве псевдонима источника идентификации, если вы используете проверку подлинности SSPI. |
    | **База DN для групп** | База выделяла название для групп. Для Azure AD `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` используйте: Пример:`OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **URL-адрес основного сервера** | Первичный контроллер домена LDAP сервер для домена.<br><br>Используйте `ldaps://hostname:port`формат . Порт обычно 636 для соединений LDAPS. <br><br>Сертификат, который устанавливает доверие к конечной точке LDAPS сервера `ldaps://` Active Directory, требуется при использовании в первичном или вторичном URL LDAP. |
    | **Url-адрес вторичного сервера** | Адрес вторичного контроллера домена LDAP- сервера, который используется для сбоя. |
    | **Выберите сертификат** | Если вы хотите использовать LDAPS с вашим сервером Active Directory LDAP или источником идентификации OpenLDAP Server, после ввода `ldaps://` в текстовом поле URL появляется кнопка «Выберите сертификат». Вторичный URL не требуется. |
    | **Пользователя** | Идентификатор пользователя в домене, который имеет минимальный доступ только для чтения к Base DN для пользователей и групп. |
    | **Пароль** | Пароль пользователя, указанный именем пользователя. |

3. Войдя в свой private Cloud vCenter после эскалации привилегий.
4. Следуйте инструкциям в [Добавлении источника идентификации в vCenter,](set-vcenter-identity.md#add-an-identity-source-on-vcenter) используя значения предыдущего шага для настройки Active Directory Azure в качестве источника идентификации.
5. Добавьте пользователей/групп из Azure AD в группы vCenter, описанные в теме VMware, [Добавьте участников в группу vCenter Single Sign-On.](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)

> [!CAUTION]
> Новые пользователи должны быть добавлены только в *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group,* *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* или, *Cloud-Global-VM-Admin-Group.*  Пользователи, добавленные в группу *администраторов,* будут удалены автоматически.  В группу администраторов должны быть *добавлены* только учетные записи служб.

## <a name="next-steps"></a>Дальнейшие действия

* [Узнайте о модели разрешения частного облака](learn-private-cloud-permissions.md)
