---
title: портал Azure URL-адреса списка надежных отправителей | Документация Майкрософт
description: Добавьте эти URL-адреса в обход сервера для взаимодействия с портал Azure и его службами.
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 9618d99495e65231de22eb719355e2ca75a17600
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218039"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Допортал Azure URL-адреса в брандмауэре или прокси-сервере.

Для обеспечения высокой производительности и подключения между локальной или глобальной сетью и облаком Azure настройте локальные устройства безопасности, чтобы обойти ограничения безопасности для URL-адресов портал Azure. Сетевые администраторы часто развертывают прокси-серверы, брандмауэры или другие устройства, чтобы обеспечить безопасность и управление доступом пользователей к Интернету. Однако правила, предназначенные для защиты пользователей, иногда могут блокировать или замедлить допустимый деловой Интернет-трафик, включая взаимодействие между вами и Azure. Для оптимизации подключения между сетью и портал Azure и ее службами рекомендуется добавить портал Azure URL-адреса списка надежных отправителей.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URL-адреса портал Azure для обхода прокси-сервера

Конечные точки URL-адреса для списка надежных отправителей портал Azure относятся к облаку Azure, в котором развернута ваша организация. Выберите облако, а затем добавьте список URL-адресов на прокси-сервер или брандмауэр, чтобы разрешить сетевой трафик к этим конечным точкам для обхода ограничений.

#### <a name="public-cloudtabpublic-cloud"></a>[Общедоступное облако](#tab/public-cloud)
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

#### <a name="us-government-cloudtabus-government-cloud"></a>[АМЕРИКАНСКОГО Облако для государственных организаций](#tab/us-government-cloud)
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

#### <a name="china-government-cloudtabchina-government-cloud"></a>[Облако для государственных учреждений Китая](#tab/china-government-cloud)
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
## <a name="next-steps"></a>Следующие шаги

Требуется адрес списка надежных отправителей IP-адресов? Скачайте список диапазонов IP-адресов центра обработки данных Microsoft Azure для вашего облака:

* [По всему миру](https://www.microsoft.com/download/details.aspx?id=56519)
* [АМЕРИКАНСКОГО Учреждений](https://www.microsoft.com/download/details.aspx?id=57063)
* [Германия](https://www.microsoft.com/download/details.aspx?id=57064)
* [Китай](https://www.microsoft.com/download/details.aspx?id=57062)

Другие службы Майкрософт используют для подключения дополнительные URL-адреса. Сведения об оптимизации сетевого подключения для служб Microsoft 365 см. в разделе [Настройка сети для Office 365](/office365/enterprise/set-up-network-for-office-365).
