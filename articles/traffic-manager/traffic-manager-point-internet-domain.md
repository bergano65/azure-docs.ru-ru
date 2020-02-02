---
title: Указание Интернет-домена для диспетчера трафика с помощью диспетчера трафика Azure
description: В этой статье вы узнаете, как направить доменное имя вашей компании на доменное имя диспетчера трафика.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: rohink
ms.openlocfilehash: d56e3fe759d2c9dbee9a8f19a6f1a030565c8e4e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938492"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Указание домена диспетчера трафика для интернет-домена компании.

Когда вы создаете профиль диспетчера трафика, Azure автоматически присваивает этому профилю имя DNS. Чтобы использовать имя зоны DNS, создайте запись DNS CNAME, которая сопоставляет доменное имя вашего профиля диспетчера трафика. Доменное имя диспетчера трафика находится в разделе **Общие** на странице конфигурации в профиле диспетчера трафика.

Например, чтобы указать имя `www.contoso.com` для имени диспетчера трафика DNS `contoso.trafficmanager.net`, создайте следующую запись ресурса DNS:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Весь трафик к *www\.contoso.com* передается в *contoso.trafficmanager.NET*.

> [!IMPORTANT]
> Нельзя направить домен второго уровня, например *contoso.com*, в домен диспетчера трафика. Стандарты протокола DNS не позволяют использовать записи CNAME для имен домена второго уровня.

## <a name="next-steps"></a>Дальнейшие действия

* [Методы маршрутизации диспетчера трафика](traffic-manager-routing-methods.md)
* [Диспетчер трафика — включение, отключение или удаление профиля диспетчера трафика](disable-enable-or-delete-a-profile.md)
* [Диспетчер трафика — отключение и включение конечной точки диспетчера трафика](disable-or-enable-an-endpoint.md)
