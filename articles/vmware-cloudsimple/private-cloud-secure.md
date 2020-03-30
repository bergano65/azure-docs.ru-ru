---
title: Решения Azure VMware от CloudSimple - Безопасное частное облако
description: Описывает, как обеспечить безопасность решений Azure VMware с помощью облачного частного облака
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4541874a9e8fc4111e5c65d02f07535c4d14f9f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565984"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Как обезопасить среду частного облака

Определите элементуправления доступа на основе ролей (RBAC) для облачных сервисов, портала CloudSimple и частного облака из Azure.  Пользователи, группы и роли для доступа к vCenter of Private Cloud определяются с помощью VMware SSO.  

## <a name="rbac-for-cloudsimple-service"></a>RBAC для службы CloudSimple

Создание сервиса CloudSimple требует роли **владельца** или **автора** в подписке Azure.  По умолчанию все владельцы и участники могут создать сервис CloudSimple и получить доступ к порталу CloudSimple для создания и управления частными облаками.  В регионе можно создать только один сервис CloudSimple.  Чтобы ограничить доступ к определенным администраторам, следуйте ниже процедуре.

1. Создание службы CloudSimple в новой **группе ресурсов** на портале Azure
2. Укажите RBAC для группы ресурсов.
3. Покупка узлов и использование той же группы ресурсов, что и сервис CloudSimple

Только пользователи, имеющие привилегии **владельца** или **вкладчика** в группе ресурсов, увидят сервис CloudSimple и запустят портал CloudSimple.

Для получения дополнительной информации [What is role-based access control (RBAC) for Azure resources](../role-based-access-control/overview.md)о RBAC см.

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC для частного облака vCenter

Пользователь `CloudOwner@cloudsimple.local` по умолчанию создается в домене vCenter SSO при создании частного облака.  Пользователь CloudOwner имеет привилегии для управления vCenter. Дополнительные источники идентификации добавляются в vCenter SSO для предоставления доступа к различным пользователям.  На vCenter настраиваются заранее определенные роли и группы, которые могут быть использованы для добавления дополнительных пользователей.

### <a name="add-new-users-to-vcenter"></a>Добавление новых пользователей в vCenter

1. [Эскалация привилегий](escalate-private-cloud-privileges.md) для **пользователя CloudOwner\@cloudsimple.local** в частном облаке.
2. Вопием в vCenter с помощью **CloudOwner\@cloudsimple.local**
3. [Добавить vCenter Одиночный знак-на пользователей](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Добавить пользователей в [vCenter одной группы регистрации.](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)

Для получения дополнительной информации о заранее определенных ролей и групп, см [CloudSimple Частное облако разрешение модели VMware vCenter](learn-private-cloud-permissions.md) статьи.

### <a name="add-new-identity-sources"></a>Добавление новых источников идентификации

Вы можете добавить дополнительные поставщики идентификационных данных для домена vCenter SSO вашего частного облака.  Поставщики идентификационных данных обеспечивают аутентификацию, а группы vCenter SSO авторизующие данные для пользователей.

* [Используйте Active Directory в качестве поставщика идентификационных данных](set-vcenter-identity.md) в Private Cloud vCenter.
* [Используйте Azure AD в качестве поставщика идентификационных данных](azure-ad.md) в Private Cloud vCenter

1. [Эскалация привилегий](escalate-private-cloud-privileges.md) для **пользователя CloudOwner\@cloudsimple.local** в частном облаке.
2. Вопием в vCenter с помощью **CloudOwner\@cloudsimple.local**
3. Добавление пользователей из поставщика идентификационных данных в [группы vCenter с одним ивением.](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)

## <a name="secure-network-on-your-private-cloud-environment"></a>Безопасная сеть в среде частного облака

Сетевая безопасность среды Private Cloud контролируется за счет обеспечения доступа к сети и управления сетевым трафиком между ресурсами.

### <a name="access-to-private-cloud-resources"></a>Доступ к ресурсам частного облака

Private Cloud vCenter и доступ к ресурсам осуществляется по безопасному сетевому подключению:

* **[Соединение ExpressRoute](on-premises-connection.md)**. ExpressRoute обеспечивает безопасное, высокоскоростное соединение с низкой задержкой в вашей среде.  Использование соединения позволяет вашим службам, сетям и пользователям получить доступ к вашему Private Cloud vCenter.
* **[Сайт-к-сайт VPN шлюз](vpn-gateway.md)**. VPN от сайта к сайту предоставляет доступ к ресурсам Private Cloud из закрытых через безопасный туннель.  Вы указываете, какие сети могут отправлять и получать сетевой трафик в ваше частное облако.
* **[Точка-к-сайт VPN шлюз](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**. Используйте VPN-соединение от точки к сайту для быстрого удаленного доступа к вашему Private Cloud vCenter.

### <a name="control-network-traffic-in-private-cloud"></a>Управление сетевым трафиком в частном облаке

Таблицы брандмауэра и правила управления сетевым трафиком в частном облаке.  Таблица брандмауэра позволяет управлять сетевым трафиком между исходной сетью или IP-адресом и сетью назначения или IP-адресом на основе сочетания правил, определенных в таблице.

1. Создайте [таблицу брандмауэра.](firewall.md#add-a-new-firewall-table)
2. [Добавьте правила](firewall.md#create-a-firewall-rule) в таблицу брандмауэра.
3. [Прикрепите стол брандмауэра к VLAN/subnet.](firewall.md#attach-vlans-subnet)
