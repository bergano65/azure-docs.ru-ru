---
title: Общие сведения о службе "Очереди Azure" — служба хранилища Azure
description: Общие сведения о службе "Очереди Azure"
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: overview
ms.date: 02/06/2019
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 544ff9d2c624ef62bf8041afd818153c1c4bfcc8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142498"
---
# <a name="what-are-azure-queues"></a>Что такое очереди Azure?

Хранилище очередей Azure — это служба для хранения большого количества сообщений, к которым можно получить доступ практически из любой точки мира с помощью вызовов с проверкой подлинности по протоколам HTTP или HTTPS. Одно сообщение очереди может быть размером до 64 КБ, а очередь может содержать миллионы сообщений до общего ограничения емкости учетной записи хранения.

## <a name="common-uses"></a>Распространенные варианты использования

Наиболее частые способы использования хранилища очередей включают:

* создание списка невыполненных работ для асинхронной обработки;
* передачу сообщений из веб-роли Azure в рабочую роль Azure.

## <a name="queue-service-concepts"></a>Основные понятия службы очередей

Служба очереди содержит следующие компоненты:

![Понятия очереди](./media/storage-queues-introduction/queue1.png)

* **Формат URL-адреса**. К очереди можно обратиться, используя следующий формат URL-адреса:   
    https://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    Следующий URL-адрес позволяет обратиться к очереди на схеме:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Учетная запись хранения**. Весь доступ к хранилищу Azure осуществляется с помощью учетной записи хранения. Сведения об емкости учетной записи хранения см. в статье [Целевые показатели масштабируемости и производительности службы хранилища Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Очередь**. Очередь содержит набор сообщений. Все сообщения должны находиться в очереди. Обратите внимание: имя очереди должно содержать только строчные символы. Дополнительные сведения см. в статье о [присвоении имен очередям и метаданным](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **Сообщение**. Сообщение в любом формате размером до 64 КБ. Сообщение может оставаться в очереди не более 7 дней.

## <a name="next-steps"></a>Дополнительная информация

* [создать учетную запись хранения;](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Начало работы с очередями с использованием .NET](storage-dotnet-how-to-use-queues.md)
