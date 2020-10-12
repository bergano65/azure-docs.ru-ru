---
title: Указание Интернет-домена для диспетчера трафика с помощью диспетчера трафика Azure
description: В этой статье вы узнаете, как направить доменное имя вашей компании на доменное имя диспетчера трафика.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: duau
ms.openlocfilehash: 93a8076f12b8f006d600cbd32ce39169f4b47c67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89392601"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Указание домена диспетчера трафика для интернет-домена компании.

Когда вы создаете профиль диспетчера трафика, Azure автоматически присваивает этому профилю имя DNS. Чтобы использовать имя зоны DNS, создайте запись DNS CNAME, которая сопоставляет доменное имя вашего профиля диспетчера трафика. Доменное имя диспетчера трафика находится в разделе **Общие** на странице конфигурации в профиле диспетчера трафика.

Например, чтобы указать имя `www.contoso.com` для имени диспетчера трафика DNS `contoso.trafficmanager.net`, создайте следующую запись ресурса DNS:

`www.contoso.com IN CNAME contoso.trafficmanager.net.`

Весь трафик к *www \. contoso.com* направляется в *contoso.trafficmanager.NET*.

> [!IMPORTANT]
> Нельзя направить домен второго уровня, например *contoso.com*, в домен диспетчера трафика. Стандарты протокола DNS не позволяют использовать записи CNAME для имен домена второго уровня.

## <a name="next-steps"></a>Дальнейшие шаги

* [Методы маршрутизации диспетчера трафика](traffic-manager-routing-methods.md)
* [Диспетчер трафика — включение, отключение или удаление профиля диспетчера трафика](disable-enable-or-delete-a-profile.md)
* [Диспетчер трафика — отключение и включение конечной точки диспетчера трафика](disable-or-enable-an-endpoint.md)
