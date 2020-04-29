---
title: Параметры портов API ретранслятора Azure | Документация Майкрософт
description: Эта статья содержит таблицу с описанием требуемой конфигурации для значений портов для Azure Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 532e3c297c42ea16b1f462a01699fc2fd71c6cce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80529114"
---
# <a name="azure-relay-port-settings"></a>Параметры порта ретрансляции Azure

В таблице ниже описаны необходимые значения параметров портов для ретранслятора Azure.

## <a name="hybrid-connections"></a>Гибридные подключения

Гибридные подключения использует WebSockets через порт 443 с TLS в качестве базового механизма транспорта, который использует только **протокол HTTPS** . 

## <a name="wcf-relays"></a>Ретрансляторы WCF
  
|Привязка|Безопасность транспорта|Порт|  
|-------------|------------------------|----------|  
|[Класс BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (клиент)|Да|HTTPS| 
|" |Нет|HTTP|  
|[Класс BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (служба)|Можно использовать|9351/HTTP|  
|[Класс NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (клиент)|Да|9351/HTTPS|  
|" |Нет|9350/HTTP|  
|[Класс NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (служба)|Можно использовать|9351/HTTP|  
|[Класс NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (служба или клиент)|Можно использовать|5671/9352/HTTP (9352/9353 при использовании гибридной среды)|  
|[Класс NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (клиент)|Да|9351/HTTPS|  
|" |Нет|9350/HTTP|  
|[Класс NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (служба)|Можно использовать|9351/HTTP|  
|[Класс WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (клиент)|Да|HTTPS|  
|" |Нет|HTTP|  
|[Класс WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (служба)|Можно использовать|9351/HTTP|  
|[Класс WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (клиент)|Да|HTTPS|  
|" |Нет|HTTP|  
|[Класс WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (служба)|Можно использовать|9351/HTTP|

## <a name="next-steps"></a>Дальнейшие шаги
Дополнительные сведения о ретрансляторе Azure доступны по следующим ссылкам:
* [Что такое ретранслятор Azure?](relay-what-is-it.md)
* [Вопросы и ответы по ретранслятору](relay-faq.md)