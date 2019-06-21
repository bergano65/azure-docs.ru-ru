---
title: Объединение списков надежных портала Azure URL-адреса | Документация Майкрософт
description: Добавить эти URL-адреса обход прокси-сервера для взаимодействия с порталом Azure и ее служб
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 87ec600a2f6c4a560ec7cbb064b561fa76e2b615
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305105"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Объединение списков надежных портала Azure URL-адресов, брандмауэр или прокси-сервера

Для высокой производительности и подключение между локальной или глобальной сети и облаком Azure настройте локальных устройств безопасности для обхода ограничений безопасности для портала Azure URL-адреса. Сетевые администраторы зачастую развертывают прокси-серверами, брандмауэрами или других устройств, чтобы помочь обезопасить и позволяют контролировать способ доступа пользователей к Интернету. Тем не менее правила, предназначенных для защиты пользователей иногда можно заблокировать или замедлить законным бизнес-Интернет-трафик, включая связи между вами и Azure. Для оптимизации подключений между вашей сетью и портала Azure и ее служб, мы рекомендуем добавить портал Azure URL-адресов для вашего списка надежных отправителей.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Обход URL-адреса для прокси-сервера портала Azure

Добавьте перечисленные ниже URL-адреса прокси-сервер или брандмауэр, чтобы разрешить сетевой трафик к этим конечным точкам, чтобы обойти ограничения:

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
* *.wpc.azureedge.net

> [!NOTE]
> Трафик к этим конечным точкам использует стандартный TCP-порты для HTTP (80) и HTTPS (443).
>
>
## <a name="next-steps"></a>Дальнейшие действия

* Требуется объединение списков надежных IP-адреса? Скачать список [диапазоны IP-адрес центра обработки данных Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* Другие службы использования в корпорации Майкрософт дополнительные URL-адреса и IP-адреса для подключения. Для оптимизации сетевого соединения для служб Microsoft 365, см. в разделе [настройки сети для Office 365](/office365/enterprise/set-up-network-for-office-365).
