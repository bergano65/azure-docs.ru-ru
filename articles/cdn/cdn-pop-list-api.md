---
title: Извлеките текущий список IP-адресов POP для Azure CDN Документы Майкрософт
description: Узнайте, как получить текущий список POP.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: a58fd7c6f50cd46ac3c34cd7e5bd329c0007e5f6
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260196"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Извлеките текущий список IP-адресов POP для Azure CDN

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Извлекайте текущий список IP-адресов Verizon POP для Azure CDN

С помощью REST API можно получить набор IP-адресов для серверов точек подключения (POP) Verizon. Эти серверы POP отправляют запросы к серверам-источникам, которые связаны с конечными точками сети доставки содержимого (CDN) Azure в профиле Verizon (**Azure CDN уровня "Стандартный" от Verizon ** или **Azure CDN уровня "Премиум" от Verizon**). Обратите внимание, что этот набор IP-адресов отличается от IP-адресов, которые будет видеть клиент при выполнении запросов к точкам POP. 

Синтаксис операции REST API для получения списка точек POP см. в статье [Edge Nodes - List](https://docs.microsoft.com/rest/api/cdn/edgenodes/list) (Пограничные узлы — список).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Извлеките текущий список IP-адресов Microsoft POP для Azure CDN

Чтобы заблокировать приложение, чтобы принимать трафик только от Azure CDN от корпорации Майкрософт, необходимо настроить IP ACLs для бэкэнда. Можно также ограничить набор принятых значений для заголовка 'X-Forwarded-Host', отправленного Azure CDN от корпорации Майкрософт. Эти шаги подробно описаны ниже:

Направьте IP ACLing для бэкэндов, чтобы принимать трафик от Azure CDN из пространства IP-адресов Microsoft и только инфраструктурных служб Azure. 

* Azure CDN от iPv4 промежуточного пространства Microsoft: 147.243.0.0/16
* Azure CDN от iPv6 резервного пространства Microsoft: 2a01:111:2050::/44

Диапазоны IP и теги служб ы иссяков можно найти [здесь](https://www.microsoft.com/download/details.aspx?id=56519)


## <a name="typical-use-case"></a>Типичный случай использования

В целях безопасности этот список IP-адресов можно использовать для принудительного выполнения запросов к серверу-источнику только с допустимой точки POP Verizon. Например, если кто-то узнает имя узла или IP-адрес сервера-источника конечной точки CDN, он сможет отправлять запросы напрямую на сервер-источник, обходя, таким образом, функции масштабирования и безопасности, предоставляемые Azure CDN. Чтобы избежать такой ситуации, IP-адреса в возвращаемом списке следует задать только как разрешенные на сервере-источнике. Чтобы иметь в распоряжении актуальный список точек POP, получайте его по меньшей мере один раз в день. 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о REST API см. в статье [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/) (REST API сети доставки содержимого Azure).
