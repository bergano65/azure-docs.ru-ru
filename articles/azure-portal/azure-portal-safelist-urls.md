---
title: портал Azure URL-адреса списка надежных отправителей | Документация Майкрософт
description: Добавьте эти URL-адреса в обход сервера для взаимодействия с портал Azure и его службами.
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3747ca7504e1a8a6bbeb6237c1b3cb2e5e4afb5b
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667471"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Допортал Azure URL-адреса в брандмауэре или прокси-сервере.

Для обеспечения высокой производительности и подключения между локальной или глобальной сетью и облаком Azure настройте локальные устройства безопасности, чтобы обойти ограничения безопасности для URL-адресов портал Azure. Сетевые администраторы часто развертывают прокси-серверы, брандмауэры или другие устройства, чтобы обеспечить безопасность и управление доступом пользователей к Интернету. Однако правила, предназначенные для защиты пользователей, иногда могут блокировать или замедлить допустимый деловой Интернет-трафик, включая взаимодействие между вами и Azure. Для оптимизации подключения между сетью и портал Azure и ее службами рекомендуется добавить портал Azure URL-адреса списка надежных отправителей.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URL-адреса портал Azure для обхода прокси-сервера

Добавьте приведенный ниже список URL-адресов для прокси сервера или брандмауэра, чтобы разрешить сетевой трафик к этим конечным точкам для обхода ограничений.

* *.aadcdn.microsoftonline-p.com
* *.aimon.applicationinsights.io
* *.azure.com
* *.azuredatalakestore.net
* *.azureedge.net
* *.exp.azure.com
* *.ext.azure.com
* *.gfx.ms
* *.account.microsoft.com
* *.hosting.portal.azure.net
* *.marketplaceapi.microsoft.com
* *.microsoftonline.com
* *.msauth.net
* *.msftauth.net
* *.portal.azure.com
* *.portalext.visualstudio.com
* *.sts.microsoft.com
* *.vortex.data.microsoft.com
* *.vscommerce.visualstudio.com
* *.vssps.visualstudio.com
* *.windows.net
* *.wpc.azureedge.net

> [!NOTE]
> Трафик к этим конечным точкам использует стандартные TCP-порты для HTTP (80) и HTTPS (443).
>
>
## <a name="next-steps"></a>Следующие шаги

* Требуется адрес списка надежных отправителей IP-адресов? Скачайте список диапазонов [IP-адресов центра обработки данных Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* Другие службы Майкрософт используют для подключения дополнительные URL-адреса. Сведения об оптимизации сетевого подключения для служб Microsoft 365 см. в разделе [Настройка сети для Office 365](/office365/enterprise/set-up-network-for-office-365).
