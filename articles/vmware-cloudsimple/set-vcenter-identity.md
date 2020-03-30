---
title: Решение Azure VMware от CloudSimple - Настройка источников идентификации vCenter в частном облаке
description: Описывает, как настроить Private Cloud vCenter для проверки подлинности с помощью Active Directory для администраторов VMware для доступа к vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5355e43ca6ac075e76a76ceb51be135cf4b62b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564029"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Настройка источников идентификации vCenter для использования Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>Об источниках идентификации VMware vCenter

VMware vCenter поддерживает различные источники идентификации для проверки подлинности пользователей, которые получают доступ к vCenter.  Ваш VCenter CloudSimple Private Cloud может быть настроен для проверки подлинности с помощью Active Directory для администраторов VMware для доступа к vCenter. Когда настройка завершена, пользователь **cloudowner** может добавить пользователей из источника идентификации в vCenter.  

Вы можете настроить домен Active Directory и контроллеры домена любым из следующих способов:

* Активные контроллеры домена каталога и домена, работающие на месте
* Контроллеры домена Active Directory и домена, работающие на Azure как виртуальные машины в подписке Azure
* Новый домен Active Directory и контроллеры доменов, работающие в вашем частном облаке
* Служба активного каталога Azure

В этом руководстве разъясняются задачи по настройке домена Active Directory и контроллеров доменов Active Directory, работающих либо на месте, либо в качестве виртуальных машин в подписках.  Если вы хотите использовать Azure AD в качестве источника идентификации, обратитесь к [Use Azure AD в качестве поставщика идентификационных](azure-ad.md) данных для vCenter на CloudSimple Private Cloud для подробных инструкций по настройке источника идентификации.

Перед [добавлением источника идентификации](#add-an-identity-source-on-vcenter)временно [обостряйте свои привилегии vCenter.](escalate-private-cloud-privileges.md)

> [!CAUTION]
> Новые пользователи должны быть добавлены только в *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group,* *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* или, *Cloud-Global-VM-Admin-Group.*  Пользователи, добавленные в группу *администраторов,* будут удалены автоматически.  Только учетные записи служб должны быть добавлены в группу *администраторов,* а учетные записи службы не должны использоваться для входиных в веб-uI vSphere.   


## <a name="identity-source-options"></a>Параметры источника идентификации

* [Добавление на территории Active Directory в качестве единого источника идентификации](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Настройка нового активного каталога в частном облаке](#set-up-new-active-directory-on-a-private-cloud)
* [Настройка активного каталога на Azure](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Добавление активного каталога On-Premises в качестве единого источника идентификации

Чтобы настроить свой предпосылок Active Directory в качестве единого источника идентификации, вам необходимо:

* [VPN-соединение](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) от сайта к сайту от вашего центра обработки данных до вашего частного облака.
* На территории DNS сервера IP добавлен в vCenter и Platform Services Controller (PSC).

Используйте эту информацию в следующей таблице при настройке домена Active Directory.

| **Параметр** | **Описание** |
|------------|-----------------|
| **Название** | Имя источника идентификации. |
| **База DN для пользователей** | База выделяемое имя для пользователей. |
| **Доменное имя** | Например, example.com. Не предоставляйте IP-адрес в этом текстовом поле. |
| **Псевдоним домена** | Имя домена NetBIOS. Добавьте имя домена Active Directory в качестве псевдонима источника идентификации, если вы используете проверку подлинности SSPI. |
| **База DN для групп** | База выделяла название для групп. |
| **URL-адрес основного сервера** | Первичный контроллер домена LDAP сервер для домена.<br><br>Используйте `ldap://hostname:port` формат `ldaps://hostname:port`или . Порт обычно 389 для соединений LDAP и 636 для соединений LDAPS. Для развертывания мультидоменных контроллеров Active Directory порт обычно радо 3268 для LDAP и 3269 для LDAPS.<br><br>Сертификат, который устанавливает доверие к конечной точке LDAPS сервера `ldaps://` Active Directory, требуется при использовании в первичном или вторичном URL LDAP. |
| **Url-адрес вторичного сервера** | Адрес вторичного контроллера домена LDAP- сервера, который используется для сбоя. |
| **Выберите сертификат** | Если вы хотите использовать LDAPS с вашим сервером Active Directory LDAP или источником идентификации OpenLDAP Server, после ввода `ldaps://` в текстовом поле URL появляется кнопка «Выберите сертификат». Вторичный URL не требуется. |
| **Пользователя** | Идентификатор пользователя в домене, который имеет минимальный доступ только для чтения к Base DN для пользователей и групп. |
| **Пароль** | Пароль пользователя, указанный именем пользователя. |

Если у вас есть информация в предыдущей таблице, вы можете добавить свой предпосылок Active Directory в качестве единого источника идентификации в vCenter.

> [!TIP]
> Более подробную информацию о едином источнике идентификации можно найти на [странице документации VMware.](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html)

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Настройка нового активного каталога в частном облаке

Вы можете настроить новый домен Active Directory в личном облаке и использовать его в качестве источника идентификации для Single Sign-On.  Домен Active Directory может быть частью существующего леса Active Directory или может быть создан как независимый лес.

### <a name="new-active-directory-forest-and-domain"></a>Новый активный справочник лес и домен

Для настройки нового леса и домена Active Directory необходимо:

* Одна или несколько виртуальных машин под управлением Microsoft Windows Server для использования в качестве контроллеров доменов для нового леса и домена Active Directory.
* Одна или несколько виртуальных машин под управлением Службы DNS для разрешения имен.

Подробные шаги можно посмотреть на [новый Windows Server 2012 Active Directory Forest.](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-)

> [!TIP]
> Для высокой доступности услуг мы рекомендуем настроить несколько контроллеров доменов и DNS-серверов.

После настройки леса и домена Active Directory можно [добавить источник идентификации в vCenter](#add-an-identity-source-on-vcenter) для нового Active Directory.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Новый домен Active Directory в существующем лесу Active Directory

Для настройки нового домена Active Directory в существующем лесу Active Directory необходимо:

* VPN-соединение от сайта к сайту с вашим местоположением Active Directory forest.
* DNS Server для решения названия существующего леса Active Directory.

Для детальных шагов смотрите [для подробных шагов— «Установка нового домена «Активный каталог 2012 Active Directory» или «Дерево».](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-)

После настройки домена Active Directory можно [добавить источник идентификации](#add-an-identity-source-on-vcenter) в vCenter для нового Active Directory.

## <a name="set-up-active-directory-on-azure"></a>Настройка active-каталога на Azure

Активный каталог, работающий на Azure, похож на Active Directory, работающий в помещении.  Для настройки Active Directory, работающего в Azure в качестве единого источника идентификации на vCenter, сервер vCenter и PSC должны иметь сетевое подключение к виртуальной сети Azure, где работают службы Active Directory.  Это подключение можно установить с [помощью виртуального сетевого соединения Azure с помощью ExpressRoute](azure-expressroute-connection.md) из виртуальной сети Azure, где активные службы каталога работают в Облачное частное облако.

После создания сетевого соединения следуйте за шагами в [Add On-Premises Active Directory в качестве единого источника идентификации,](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) чтобы добавить его в качестве источника идентификации.  

## <a name="add-an-identity-source-on-vcenter"></a>Добавление источника идентификации на vCenter

1. [Эскалация привилегий](escalate-private-cloud-privileges.md) в вашем частном облаке.

2. Войти в vCenter для вашего частного облака.

3. Выберите **администрацию > дома**.

    ![Администрирование](media/OnPremAD01.png)

4. Выберите **единый знак на конфигурации >**.

    ![Единый вход](media/OnPremAD02.png)

5. Откройте вкладку **«Источники идентификации»** и нажмите **+** кнопку добавления нового источника идентификации.

    ![Источники идентификации](media/OnPremAD03.png)

6. Выберите **Active Directory в качестве сервера LDAP** и нажмите **далее.**

    ![Active Directory](media/OnPremAD04.png)

7. Укажите параметры источника идентификации для среды и нажмите **Кнопка Next**.

    ![Active Directory](media/OnPremAD05.png)

8. Просмотрите настройки и нажмите **Кнопка Готово**.
