---
title: Направление Интернет-домена компании на имя домена диспетчера трафика Azure
description: В этой статье вы узнаете, как направить доменное имя вашей компании на доменное имя диспетчера трафика.
services: traffic-manager
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
ms.openlocfilehash: 77a5fbab6ecda910750ab2b8bae987e77607223a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57903520"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Указание домена диспетчера трафика для интернет-домена компании.

Когда вы создаете профиль диспетчера трафика, Azure автоматически присваивает этому профилю имя DNS. Чтобы использовать имя зоны DNS, создайте запись DNS CNAME, которая сопоставляет доменное имя вашего профиля диспетчера трафика. Доменное имя диспетчера трафика находится в разделе **Общие** на странице конфигурации в профиле диспетчера трафика.

Например, чтобы указать имя `www.contoso.com` для имени диспетчера трафика DNS `contoso.trafficmanager.net`, создайте следующую запись ресурса DNS:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Весь трафик, направленный к *www\.contoso.com* перенаправляется на *contoso.trafficmanager.net*.

> [!IMPORTANT]
> Нельзя направить домен второго уровня, например *contoso.com*, в домен диспетчера трафика. Стандарты протокола DNS не позволяют использовать записи CNAME для имен домена второго уровня.

## <a name="next-steps"></a>Дальнейшие действия

* [Методы маршрутизации диспетчера трафика](traffic-manager-routing-methods.md)
* [Диспетчер трафика — включение, отключение или удаление профиля диспетчера трафика](disable-enable-or-delete-a-profile.md)
* [Диспетчер трафика — отключение и включение конечной точки диспетчера трафика](disable-or-enable-an-endpoint.md)
