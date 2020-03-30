---
title: Управление решением Azure VMware с помощью облачного частного облака
description: Описывает возможности, доступные для управления ресурсами и активностью облачных технологий CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 620c0226d3aca907352658ebbe1b94c7673d91cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014833"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Управление ресурсами и активностью частных облаков

Частные облака управляются с портала CloudSimple.  Проверьте состояние, доступные ресурсы, активность в частном облаке и другие настройки с портала CloudSimple.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войти на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Доступ к порталу CloudSimple

Доступ к [порталу CloudSimple](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Посмотреть список частных облаков

Вкладка **Private Clouds** на странице **Ресурсов** перечисляет все частные облака в вашей подписке. Информация включает имя, количество кластеров vSphere, местоположение, текущее состояние частного облака и информацию о ресурсах.

![Страница частного облака](media/manage-private-cloud.png)

Выберите частное облако для получения дополнительной информации и действий.

## <a name="private-cloud-summary"></a>Резюме частного облака

Посмотреть полное резюме выбранного частного облака.  Краткая страница включает dNS-серверы, развернутые в приватном облаке.  Вы можете настроить переадресировку DNS с закрытых DNS-серверов на свои серверы Private Cloud DNS.  Для получения дополнительной информации о dNS-пересылке см. [Накрешимость DNS для разрешения имен для Private Cloud vCenter из предварительного.](https://docs.azure.cloudsimple.com/on-premises-dns-setup/)

![Резюме частного облака](media/private-cloud-summary.png)

### <a name="available-actions"></a>Доступные действия

* [Запуск vSphere клиента](https://docs.azure.cloudsimple.com/vsphere-access/). Доступ к vCenter для этого частного облака.
* [Покупка узлов](create-nodes.md). Добавьте узлы в это приватное облако.
* [Расширить](expand-private-cloud.md). Добавьте узлы в это приватное облако.
* **Освежите**. Обновление информации на этой странице.
* **Удалить**. Вы можете удалить приватное облако в любое время. **Прежде чем удалять, убедитесь, что вы резервное копирование всех систем и данных.** Удаление приватного облака удаляет все вычисления, конфигурацию vCenter и данные. Нажмите **Удалить** в разделе резюме для выбранного частного облака. После удаления все данные Private Cloud стираются в безопасном, строго совместимом процессе удаления.
* [Изменение привилегий vSphere](escalate-private-cloud-privileges.md).  Обостряйте свои привилегии в этом частном облаке.

## <a name="private-cloud-vlanssubnets"></a>Частные облачные VLANS/подсети

Просмотр списка определенных VLAN/подсетей для выбранного частного облака.  Список включает в себя управление VLANs/subnets, созданные при создании частного облака.

![Частное облако - VLANs/Subnets](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Доступные действия

* [Добавить VLANS/Subnets](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Добавьте VLAN/подмножество в это приватное облако.

Выберите VLAN/Subnet для следующих действий
* [Прикрепите стол брандмауэра.](https://docs.azure.cloudsimple.com/firewall/) Прикрепите стол брандмауэра к этому частному облаку.
* **Изменить**
* **Удаление** (только пользовательские VLANs/Subnets)

## <a name="private-cloud-activity"></a>Деятельность в частном облаке

Просмотр следующей информации для выбранного частного облака.  Информация о действиях представляет собой отфильтрованный список всех действий для выбранного частного облака.  На этой странице отображаются до 25 последних действий.

* Последние оповещения
* Последние события
* Последние задачи
* Недавний аудит

![Частное облако - Деятельность](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Облачные стойки

Облачные стойки являются строительными блоками вашего частного облака. Каждая стойка обеспечивает единицу емкости. CloudSimple автоматически настраивает облачные стойки на основе выбора при создании или расширении частного облака.  Просмотр полного списка облачных стоек, включая приватное облако, к которым присваивается каждый из них.

![Частное облако - Облачные стойки](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Сеть управления vSphere

Список ресурсов управления VMware и виртуальных машин, которые в настоящее время настроены на private Cloud. Информация включает в себя версию программного обеспечения, полностью квалифицированное доменное имя (ФЗДН) и IP-адрес ресурсов.

![Частное облако - сеть управления vSphere](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Использование виртуальных машин VMware в Azure](quickstart-create-vmware-virtual-machine.md)
* Узнайте больше о [частных облаках](cloudsimple-private-cloud.md)