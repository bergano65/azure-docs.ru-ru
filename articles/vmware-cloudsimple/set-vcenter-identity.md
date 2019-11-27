---
title: Решение VMware для Azure от Клаудсимпле — Настройка источников удостоверений vCenter в частном облаке
description: Описание настройки частного облака vCenter для проверки подлинности с помощью Active Directory для администраторов VMware для доступа к vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9d2986acc47087c267193eee43136e030abcc422
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990320"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Настройка источников удостоверений vCenter для использования Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>Сведения об источниках удостоверений VMware vCenter

VMware vCenter поддерживает различные источники удостоверений для проверки подлинности пользователей, обращающихся к vCenter.  Вы можете настроить для Клаудсимпле частного облака vCenter проверку подлинности с помощью Active Directory, чтобы ваши администраторы VMware могли получить доступ к vCenter. После завершения установки пользователь **клаудовнер** может добавить пользователей из источника удостоверений в vCenter.  

Вы можете настроить Active Directory домена и контроллеров домена одним из следующих способов.

* Active Directory контроллеры домена и домена, работающих в локальной среде
* Active Directory контроллеров домена и домена, работающих в Azure в качестве виртуальных машин в подписке Azure;
* Новые Active Directory домены и контроллеры домена, работающие в частном облаке
* Служба Azure Active Directory

В этом учебнике объясняются задачи настройки Active Directory контроллеров домена и домена, работающих локально или в качестве виртуальных машин в подписках.  Если вы хотите использовать Azure AD в качестве источника удостоверения, обратитесь к статье [Использование Azure AD в качестве поставщика удостоверений для vCenter в Клаудсимпле частном облаке](azure-ad.md) для получения подробных инструкций по настройке источника удостоверений.

Прежде чем [добавлять источник удостоверений](#add-an-identity-source-on-vcenter), необходимо временно [эскалировать свои привилегии vCenter](escalate-private-cloud-privileges.md).

> [!CAUTION]
> Новые пользователи должны быть добавлены только в *облако-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-* Admin-Group, Cloud-Global- *Network-* Admin-Group или *Cloud-Global-ВМ-Admin-Group*.  Пользователи, добавленные в группу *администраторов* , будут удалены автоматически.  Только учетные записи служб должны быть добавлены в группу *администраторов* .  


## <a name="identity-source-options"></a>Параметры источника удостоверений

* [Добавление локального Active Directory в качестве источника удостоверений единого входа](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Настройка новых Active Directory в частном облаке](#set-up-new-active-directory-on-a-private-cloud)
* [Настройка Active Directory в Azure](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Добавление локального Active Directory в качестве источника удостоверений единого входа

Чтобы настроить локальный Active Directory как источник удостоверений единого входа, вам потребуется:

* [VPN-подключение типа "сеть — сеть](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) " из локального центра обработки данных к частному облаку.
* IP-адрес локального DNS-сервера добавлен в vCenter и контроллер служб платформы (PSC).

При настройке домена Active Directory используйте сведения из следующей таблицы.

| **Параметр** | **Описание** |
|------------|-----------------|
| **имя** | Имя источника удостоверений. |
| **Базовое DN для пользователей** | Базовое различающееся имя для пользователей. |
| **Доменное имя** | FDQN домена, например example.com. В этом текстовом поле не следует указывать IP-адрес. |
| **Псевдоним домена** | NetBIOS-имя домена. Если используются проверки подлинности SSPI, добавьте NetBIOS-имя домена Active Directory в качестве псевдонима источника удостоверений. |
| **Базовое DN для групп** | Базовое различающееся имя для групп. |
| **URL-адрес основного сервера** | Сервер LDAP основного контроллера домена для домена.<br><br>Используйте формат `ldap://hostname:port` или `ldaps://hostname:port`. Порт обычно 389 для подключений LDAP и 636 для подключений LDAPs. Для Active Directory развертываний нескольких контроллеров домена порт обычно 3268 для LDAP и 3269 для LDAPs.<br><br>Сертификат, устанавливающий отношение доверия для конечной точки LDAPs сервера Active Directory, необходим при использовании `ldaps://` в первичном или дополнительном URL-адресе LDAP. |
| **URL-адрес вторичного сервера** | Адрес LDAP-сервера вторичного контроллера домена, который используется для отработки отказа. |
| **Выбор сертификата** | Если вы хотите использовать LDAPs с сервером Active Directory LDAP или источником удостоверений сервера OpenLDAP, то после ввода `ldaps://` в текстовом поле URL-адрес появится кнопка выбрать сертификат. Дополнительный URL-адрес не требуется. |
| **Имя пользователя** | Идентификатор пользователя в домене, который имеет минимальный доступ только для чтения к базовому DN для пользователей и групп. |
| **Пароль** | Пароль пользователя, заданного параметром username. |

При наличии сведений в предыдущей таблице можно добавить локальный Active Directory как источник удостоверений единого входа в vCenter.

> [!TIP]
> Дополнительные сведения об источниках удостоверений единого входа можно найти на [странице документации по VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html).

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Настройка новых Active Directory в частном облаке

Вы можете настроить новый домен Active Directory в частном облаке и использовать его в качестве источника удостоверений для единого входа.  Домен Active Directory может быть частью существующего Active Directory леса или можно настроить как независимый лес.

### <a name="new-active-directory-forest-and-domain"></a>Новый Active Directory лес и домен

Чтобы настроить новый лес и домен Active Directory, вам потребуется:

* Одна или несколько виртуальных машин под управлением Microsoft Windows Server для использования в качестве контроллеров домена для нового леса Active Directory и домена.
* Одна или несколько виртуальных машин, на которых запущена служба DNS, для разрешения имен.

Подробные инструкции см. [в статье Установка нового леса Active Directory Windows Server 2012](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) .

> [!TIP]
> Для обеспечения высокой доступности служб рекомендуется настроить несколько контроллеров домена и DNS-серверов.

После настройки леса Active Directory и домена можно [Добавить источник удостоверений в vCenter](#add-an-identity-source-on-vcenter) для нового Active Directory.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Новый домен Active Directory в существующем Active Directory лесу

Чтобы настроить новый домен Active Directory в существующем Active Directory лесу, вам потребуется:

* VPN-подключение типа "сеть — сеть" к расположению леса Active Directory.
* DNS-сервер для разрешения имени существующего леса Active Directory.

Подробные инструкции см. [в разделе Установка нового дочернего или доменного домена Windows Server 2012 Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) .

После настройки домена Active Directory можно [Добавить источник удостоверений в vCenter](#add-an-identity-source-on-vcenter) для нового Active Directory.

## <a name="set-up-active-directory-on-azure"></a>Настройка Active Directory в Azure

Active Directory, выполняемые в Azure, похожи на Active Directory, работающие в локальной среде.  Чтобы настроить Active Directory, выполняющиеся в Azure как источник удостоверений единого входа в vCenter, сервер vCenter и контроллер PSC должны иметь сетевое подключение к виртуальной сети Azure, в которой работают службы Active Directory.  Это подключение можно установить с помощью [подключения к виртуальной сети Azure с помощью ExpressRoute](azure-expressroute-connection.md) из виртуальной сети Azure, в которой службы Active Directory работают в Клаудсимпле частном облаке.

После установки сетевого подключения выполните действия, описанные в разделе [Добавление локального Active Directory как источника удостоверений единого входа](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) , чтобы добавить его в качестве источника удостоверений.  

## <a name="add-an-identity-source-on-vcenter"></a>Добавление источника удостоверений в vCenter

1. [Эскалировать привилегии](escalate-private-cloud-privileges.md) в частном облаке.

2. Войдите в vCenter для частного облака.

3. Выберите **домашняя > администрирование**.

    ![Administration](media/OnPremAD01.png)

4. Выберите **конфигурация > единого входа**.

    ![Единый вход](media/OnPremAD02.png)

5. Откройте вкладку **источники удостоверений** и нажмите кнопку **+** , чтобы добавить новый источник удостоверений.

    ![Источники удостоверений](media/OnPremAD03.png)

6. Выберите **Active Directory в качестве сервера LDAP** и нажмите кнопку **Далее**.

    ![Active Directory](media/OnPremAD04.png)

7. Укажите параметры источника удостоверений для своей среды и нажмите кнопку **Далее**.

    ![Active Directory](media/OnPremAD05.png)

8. Проверьте параметры и нажмите кнопку **Готово**.
