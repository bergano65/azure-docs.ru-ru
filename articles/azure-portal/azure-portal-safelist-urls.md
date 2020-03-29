---
title: Безопасные URL-адреса портала Azure на брандмауэре или прокси-сервере
description: Добавьте эти URL-адреса в обход прокси-сервера для связи с порталом Azure и его сервисами
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900656"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Безопасные URL-адреса портала Azure на брандмауэре или прокси-сервере

Можно настроить устройства безопасности на месте, чтобы обойти ограничения безопасности для URL-адресов портала Azure. Эта конфигурация может повысить производительность и связь между локальной или широкой сетью и облаком Azure.

Сетевые администраторы часто развертывают прокси-серверы, брандмауэры или другие устройства. Эти устройства помогают обеспечить безопасность и дать контроль над тем, как пользователи получают доступ к Интернету. Правила, предназначенные для защиты пользователей, иногда могут блокировать или замедлять легальный интернет-трафик, связанный с бизнесом. Этот трафик включает в себя связь между вами и Azure. Чтобы оптимизировать подключение сети к порталу Azure и ее сервисам, рекомендуем добавить URL-адреса портала Azure в свой список безопасности.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URL-адреса портала Azure для обхода прокси

Конечные точки URL-адреса для безопасного для портала Azure специфичны для облака Azure, где развернута ваша организация. Чтобы сетевой трафик в этих конечных точках обходить ограничения, выберите облако. Затем добавьте список URL-адресов на прокси-сервер или брандмауэр.

#### <a name="public-cloud"></a>[Публичное облако](#tab/public-cloud)

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

#### <a name="us-government-cloud"></a>[Облако правительства США](#tab/us-government-cloud)

```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[Облако правительства Китая](#tab/china-government-cloud)

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
> Трафик на эти конечные точки использует стандартные порты TCP для HTTP (80) и HTTPS (443).
>
>
## <a name="next-steps"></a>Дальнейшие действия

Необходимо безопасно список IP-адресов? Загрузите список IP-диапазонов Центра обработки данных Microsoft Azure для облака:

* [Весь мир](https://www.microsoft.com/download/details.aspx?id=56519)
* [Правительство США](https://www.microsoft.com/download/details.aspx?id=57063)
* [Германия](https://www.microsoft.com/download/details.aspx?id=57064)
* [Китай](https://www.microsoft.com/download/details.aspx?id=57062)

Другие службы Майкрософт используют дополнительные URL-адреса и IP-адреса для подключения. Для оптимизации подключения к сети для служб Microsoft 365 можно [настроить сеть для Office 365.](/office365/enterprise/set-up-network-for-office-365)
