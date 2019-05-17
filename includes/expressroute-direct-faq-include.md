---
title: включение файла
description: включение файла
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 1bcadc18172535649a0ceb482939ca6a75477e25
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794772"
---
### <a name="what-is-expressroute-direct"></a>Что такое ExpressRoute Direct?

ExpressRoute Direct дает пользователям возможность подключения непосредственно к глобальной сети корпорации Майкрософт в стратегически распределенных по всему миру расположениях пиринга. ExpressRoute Direct обеспечивает двойное подключение со скоростью 100 Гбит/с, которое при масштабировании поддерживает подключение "активный — активный". 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Как клиенты подключаются к ExpressRoute Direct? 

Клиентам нужно обратиться к местным операторам и поставщикам услуг совместного размещения, чтобы получить возможность подключаться к маршрутизаторам ExpressRoute и использовать преимущества ExpressRoute Direct.

### <a name="what-locations-currently-support-expressroute-direct"></a>Какие расположения в настоящее время поддерживает ExpressRoute Direct? 

Доступные порты будет динамическими, с их помощью можно будет просмотреть емкость в PowerShell. Ниже приведены поддерживаемые расположения. *Этот список расположений может измениться*.

* Амстердам
* Канберра
* Чикаго
* Вашингтон, округ Колумбия
* Даллас 
* Гонконг (ОАР)
* Лондон
* Лос-Анджелес
* Нью-Йорк
* Париж
* Перт
* Торонто
* Сан-Антонио
* Сиэтл;
* Сеул
* Кремниевая долина
* Сингапур 
* Сидней

### <a name="what-is-the-sla-for-expressroute-direct"></a>Что такое Соглашение об уровне обслуживания для ExpressRoute Direct?

ExpressRoute Direct будет использовать то же [корпоративное соглашение, что и ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Для каких сценариев клиентам следует рассмотреть использование ExpressRoute Direct?  

ExpressRoute Direct предоставляет клиентам пары прямых портов (100 Гбит/с) для подключения к глобальной магистральной сети Майкрософт. Клиенты могут получить наибольшие преимущества в следующих сценариях: интенсивный прием данных, физическая изоляция поднадзорных рынков и выделенная емкость в случае скачкообразной нагрузки (например, рендеринг). 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Какая модель выставление счетов используется для ExpressRoute Direct? 

Пара портов ExpressRoute Direct оплачивается по фиксированному тарифу. Каналы ценовой категории "Стандартный" будут предоставлены без дополнительных часов, а за каналы ценовой категории "Премиум" будет взиматься небольшая плата за надстройку. Исходящий трафик для каждого канала будет оплачиваться отдельно, в соответствии с зоной, в которой расположен пиринг.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>В каком случае начала выставления счетов для пар портов ExpressRoute Direct?

Пары портов ExpressRoute Direct оплачиваются через 45 дней после создания ресурса ExpressRoute Direct или при включении 1 или обеих ссылок, в зависимости от того, что наступит раньше. Предоставляется 45-дневный льготный период, чтобы клиенты могли завершить процесс перекрестного подключения с поставщиком расположения.
