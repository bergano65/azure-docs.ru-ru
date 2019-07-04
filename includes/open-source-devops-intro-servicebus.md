---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185977"
---
[Служебная шина Azure](/azure/service-bus-messaging/service-bus-messaging-overview) — это брокер [интеграции](https://azure.microsoft.com/product-categories/integration/) сообщений корпоративного уровня. Служебная шина поддерживает два типа связи: очереди и разделы. 

Очереди поддерживают асинхронную связь между приложениями. Приложение отправляет сообщения в очередь, в которой они хранятся. Затем принимающее приложение подключается к очереди и считывает сообщения.

Разделы поддерживают шаблон публикации-подписки, который позволяет связь "один-ко-многим" между источником и получателями сообщений.