---
title: Создание VLANs/подсетей - Решение Azure VMware от CloudSimple
description: Решения Azure VMware от CloudSimple - описывает, как создавать и управлять VLANs/подсетями для ваших частных облаков, а затем применять правила брандмауэра.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0160513eb9abca54adbc3819b982348dc202c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77566001"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>Создание и управление VLAN/подсетями для ваших частных облаков

Откройте вкладку VLAN/Subnets на странице Сети для создания и управления VLANs/subnets для ваших частных облаков. После создания VLAN/subnet можно применить правила брандмауэра.

## <a name="create-a-vlansubnet"></a>Создание VLAN/субсети

1. [Получите доступ к порталу CloudSimple](access-cloudsimple-portal.md) и выберите **сеть** в боковом меню.
2. Выберите **VLANs/подсети.**
3. Нажмите **Создать VLAN / Subnet**.

    ![Страница VLAN/subnet](media/vlan-subnet-page.png)

4. Выберите частное облако для новой VLAN/subnet.
5. Введите идентификатор VLAN.
6. Введите имя подсети.
7. Для включения в систему вхром на VLAN (субнет) укажите диапазон подсети CIDR. Убедитесь, что диапазон CIDR не пересекается ни с одной из ваших подсетей, подсетями Azure или подсетью шлюза.
8. Щелкните элемент **Отправить**.

    ![Создание VLAN/субсети](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> На частное облако квота составляет 30 VLAN. Эти ограничения могут быть увеличены, [обратившись в службу поддержки.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Используйте информацию VLAN для создания распределенной группы портов в vSphere

Чтобы создать распределенную группу портов в vSphere, следуйте инструкциям в теме VMware 'Добавить распределенную группу портов' в <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">vSphere Networking Guide.</a> При настройке распределенной группы портов предоставьте информацию VLAN из конфигурации CloudSimple.

![Распределенная группа портов](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Выберите таблицу брандмауэра

Таблицы брандмауэра и связанные с ними правила определяются на странице **таблиц сети > брандмауэра.** Чтобы выбрать таблицу брандмауэра, чтобы применить к VLAN/subnet для частного облака, выберите **вложение таблицы Брандуолла** VLAN/subnet на странице **VLAN/Subnets.** Ознакомиться с [таблицами firewall](firewall.md) можно ознакомиться с инструкциями по настройке таблиц брандмауэра и определению правил.

![Ссылка на таблицу брандмауэра](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Подсеть может быть связана с одной таблицей брандмауэра. Таблица брандмауэра может быть связана с несколькими подсетями.

## <a name="edit-a-vlansubnet"></a>Изналить VLAN/субнет

Чтобы отсеять настройки для VLAN/Subnet, выберите его на странице **VLAN/Subnets** и нажмите значок **Edit.** Вносить изменения и нажмите **Submet**.

## <a name="delete-a-vlansubnet"></a>Удалить VLAN/субнет

Чтобы удалить VLAN/Subnet, выберите его на странице **VLAN/Subnets** и нажмите значок **Удалить.** **Нажмите Удалить,** чтобы подтвердить.
