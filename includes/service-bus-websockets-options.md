---
title: включить файл
description: включить файл
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: fe205524c5aef6a43771cc09c810da217678d108
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022152"
---
Параметр протокола AMQP-over-WebSockets выполняется через порт TCP 443, как и HTTP/REST API, но в противном случае он функционально идентичен обычному AMQP. Этот параметр имеет несколько более высокие начальные задержки при подключении из-за дополнительных обращений к подтверждению и немного больше ресурсов, чем компромисс для совместного использования HTTPS-порта. Если выбран этот режим, для обмена данными достаточно TCP-порта 443. Следующие параметры позволяют выбрать режим AMQP или AMQP WebSockets.

| Язык | Параметр   |
| -------- | ----- |
| .NET     | Свойство [сервицебусконнектион. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype?view=azure-dotnet) с [TransportType. AMQP](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) или [TransportType. амкпвебсоккетс](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) |
| Java     | [com. Microsoft. Azure. servicebus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings?view=azure-java-stable) с [com. Microsoft. Azure. servicebus. примитивы. TransportType. AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) или [com.Microsoft.Azure.servicebus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) |
| Узел  | [Сервицебусклиентоптионс](/javascript/api/@azure/service-bus/servicebusclientoptions?view=azure-node-latest) имеет `webSocket` аргумент конструктора. |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) с [TransportType. AMQP](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) или [TransportType. амкповервебсоккет](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |