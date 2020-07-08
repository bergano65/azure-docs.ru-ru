---
title: Добавление URL-адресов портала Azure в список надежных адресов в брандмауэре или на прокси-сервере
description: Добавьте эти URL-адреса в обход сервера для взаимодействия с портал Azure и его службами.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 04/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 67ccf6e2f113c0ec72cfcbccedf88017e452ec4b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361665"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Добавление URL-адресов портала Azure в список надежных адресов в брандмауэре или на прокси-сервере

Вы можете настроить локальные устройства безопасности, чтобы обойти ограничения безопасности для URL-адресов портал Azure. Такая конфигурация может повысить производительность и возможность подключения между локальной или глобальной сетью и облаком Azure.

Сетевые администраторы часто развертывают прокси-серверы, брандмауэры или другие устройства. Эти устройства обеспечивают безопасность и предоставляют контроль над доступом пользователей к Интернету. Правила, предназначенные для защиты пользователей, иногда могут блокировать или замедлить подлинный бизнес-трафик, связанный с бизнесом. Этот трафик включает взаимодействие между вами и Azure. Для оптимизации подключения между сетью и портал Azure и ее службами рекомендуется добавить портал Azure URL-адреса списка надежных отправителей.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URL-адреса портал Azure для обхода прокси-сервера

Конечные точки URL-адреса для списка надежных отправителей портал Azure относятся к облаку Azure, в котором развернута ваша организация. Чтобы разрешить сетевой трафик к этим конечным точкам для обхода ограничений, выберите Облако. Затем добавьте список URL-адресов в брандмауэр.

#### <a name="public-cloud"></a>[Общедоступное облако](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[Облако для государственных организаций США](#tab/us-government-cloud)

```
*.applicationinsights.us
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[Облако для государственных учреждений Китая](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> Трафик к этим конечным точкам использует стандартные TCP-порты для HTTP (80) и HTTPS (443).
>
>
