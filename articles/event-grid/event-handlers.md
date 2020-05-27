---
title: Обработчики событий службы "Сетка событий Azure"
description: В статье описаны обработчики событий, поддерживаемые в службе "Сетка событий Azure". Служба автоматизации Azure, Функции Azure, Центры событий Azure, гибридные подключения, Logic Apps, Служебная шина Azure, Хранилище очередей, веб-перехватчики.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: c29526bdee5ad70b3acb23117ee003877f52a55e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592463"
---
# <a name="event-handlers-in-azure-event-grid"></a>Обработчики событий в службе "Сетка событий Azure"
Обработчик событий — это место, куда отправляются события. Обработчик выполняет последующую обработку полученного события. Некоторые службы Azure автоматически настроены для обработки событий. Также для этого можно использовать любой веб-перехватчик. Для обработки событий, веб-перехватчик не обязательно размещать в Azure. Сетка событий поддерживает только конечные точки веб-перехватчиков HTTPS.

## <a name="supported-event-handlers"></a>Поддерживаемые обработчики событий
Ниже перечислены поддерживаемые обработчики событий. 

- [Веб-перехватчики](handler-webhooks.md). Модули Runbook службы автоматизации Azure и Logic Apps поддерживаются с помощью веб-перехватчиков. 
- [Функции Azure](handler-functions.md)
- [Центры событий](handler-event-hubs.md)
- [Гибридные подключения Relay](handler-relay-hybrid-connections.md)
- [Очереди и разделы Служебной шины Azure](handler-service-bus.md)
- [Очереди хранилища](handler-storage-queues.md)

## <a name="next-steps"></a>Дальнейшие действия
- Общие сведения о службе "Сетка событий" см. в разделе [Общие сведения о службе "Сетка событий Azure"](overview.md).
