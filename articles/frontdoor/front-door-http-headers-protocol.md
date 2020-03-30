---
title: Поддержка протокола для заголовков HTTP в передней двери Azure Документы Майкрософт
description: В этой статье описаны протоколы заголовка HTTP, которые поддерживает Front Door.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: bb1de5d51afd01cf0aa519f12aa3665bee804efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471682"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Поддержка протокола для заголовков HTTP в передней двери Azure
В этой статье излагается протокол, который поддерживает Front Door с частями пути вызова (см. изображение). В следующих разделах представлена дополнительная информация о заголовках HTTP, поддерживаемых Front Door.

![Протокол заголовков заголовков Azure Front Door HTTP][1]

>[!IMPORTANT]
>Front Door не сертифицирует заголовки HTTP, которые не описаны здесь.

## <a name="client-to-front-door"></a>Клиент — Front Door
Front Door принимает большинство заголовков из входящего запроса без их изменения. Некоторые зарезервированные заголовки удаляются из входящего запроса при отправке, включая заголовки с префиксом X-FD-.

## <a name="front-door-to-backend"></a>Front Door — серверная система

Передняя дверь включает заголовки из входящего запроса, если не удалены из-за ограничений. Передняя дверь также добавляет следующие заголовки:

| Заголовок  | Пример и описание |
| ------------- | ------------- |
| Via |  Через: 1.1 Лазурный произвля </br> Front Door добавляет версию HTTP клиента, за которой следует *Azure,* как значение для заголовка Via. Этот заголовок указывает на версию HTTP клиента и что Front Door был промежуточным получателем запроса между клиентом и бэкэндом.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Представляет IP-адрес клиента, связанный с обрабатываемым запросом. Например, запрос, поступающий от прокси-сервера, может добавить заголовок X-Forwarded-For для указания IP-адреса исходного вызываемого абонента. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Представляет IP-адрес гнезда, связанный с подключением TCP, с которого исходит текущий запрос. IP-адрес клиента может быть неравен IP-адресу гнезда, поскольку он может быть произвольно перезаписан пользователем.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV-XAAAABKMMOjBv2NT4TY6ССВД0zV1NURHRTA2MTANDD3YzgyY2-tMzYwYS00YTU0LTk0YzMtNW-NnzA3Nj-3Nzgz </br> Уникальная строка, идентифицирует запрос, обслуживаемый Front Door. Он используется для поиска журналов доступа и имеет решающее значение для устранения неполадок.|
| X-Azure-RequestChain |  X-Azure-RequestChain: хмель No1 </br> Заголовок, который использует Front Door для обнаружения циклов запросов, и пользователи не должны зависеть от него. |
| X-Forwarded-For | X-Forwarded-For: 127.0.0.1 </br> Поле заголовка X-Forwarded-For (XFF) HTTP часто определяет исходящий IP-адрес клиента, подключенного к веб-серверу через прокси-сервер HTTP или балансилер нагрузки. Если есть существующий заголовок XFF, то Front Door прикладает клиентскую розетку IP к нему или добавляет заголовок XFF с IP-адресом клиента. |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> Поле заголовка X-Forwarded-Host HTTP является распространенным методом, используемым для идентификации исходного узла, запрошенного клиентом в заголовке запроса Host HTTP. Это связано с тем, что имя хоста от Front Door может отличаться для сервера бэкэнда, обрабатывая запрос. |
| X-Forwarded-Proto | X-Forwarded-Proto: http </br> Поле заголовка X-Forwarded-Proto HTTP часто используется для определения исходного протокола запроса HTTP, поскольку Front Door, основанный на конфигурации, может общаться с бэкэндом с помощью HTTPS. Это верно, даже если запрос на обратный прокси HTTP. |
| X-FD-HealthProbe | X-FD-HealthProbe HTTP заголовок поле используется для идентификации зонда работоспособности от передней двери. Если этот заголовок установлен на 1, запрос зонд аработоспособности. Вы можете использовать, когда хотите, чтобы строгий доступ от конкретного front Door с X-Forwarded-Host заголовок поле. |

## <a name="front-door-to-client"></a>Front Door — клиент

Любые заголовки, отправленные в Front Door из бэкэнда, также передаются клиенту. Ниже приведены заголовки, отправленные от Front Door клиентам.

| Заголовок  | Пример |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV-XAAAABKMMOjBv2NT4TY6ССВД0zV1NURHRTA2MTANDD3YzgyY2-tMzYwYS00YTU0LTk0YzMtNW-NnzA3Nj-3Nzgz* </br> Это уникальная эталонная строка, которая идентифицирует запрос, обслуженный Front Door. Это имеет решающее значение для устранения неполадок, поскольку он используется для поиска журналов доступа.|

## <a name="next-steps"></a>Дальнейшие действия

- [Создание передней двери](quickstart-create-front-door.md)
- [Как работает передняя дверь](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
