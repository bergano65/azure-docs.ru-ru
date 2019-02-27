---
title: включение файла
description: включение файла
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 02/19/2018
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 9e16c3bb4e4e5ba384bdc664e2c056acb0b70caf
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56448234"
---
### <a name="what-is-expressroute-direct"></a>Что такое ExpressRoute Direct?

ExpressRoute Direct дает пользователям возможность подключения непосредственно к глобальной сети корпорации Майкрософт в стратегически распределенных по всему миру расположениях пиринга. ExpressRoute Direct обеспечивает двойное подключение со скоростью 100 Гбит/с, которое при масштабировании поддерживает подключение "активный — активный". 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Как клиенты подключаются к ExpressRoute Direct? 

Клиентам нужно обратиться к местным операторам и поставщикам услуг совместного размещения, чтобы получить возможность подключаться к маршрутизаторам ExpressRoute и использовать преимущества ExpressRoute Direct.

### <a name="what-locations-will-the-100-gbps-expressroute-direct-be-available-for-public-preview"></a>В каких расположениях будет предоставляться общедоступная предварительная версия ExpressRoute Direct (100 Гбит/с)? 

На этапе общедоступной предварительной версии пиринга ExpressRoute будет поддерживаться ограниченное число расположений. Доступные порты будет динамическими, с их помощью можно будет просмотреть емкость в PowerShell. Ниже приведены поддерживаемые расположения. *Этот список расположений может измениться*.

* Амстердам
* Канберра
* Чикаго
* Вашингтон, округ Колумбия
* Даллас 
* Гонконг, САР
* Лос-Анджелес
* Нью-Йорк
* Париж
* Сан-Антонио
* Кремниевая долина
* Сингапур 

### <a name="what-is-the-sla-for-expressroute-direct"></a>Что такое Соглашение об уровне обслуживания для ExpressRoute Direct?

ExpressRoute Direct будет использовать то же [корпоративное соглашение, что и ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Для каких сценариев клиентам следует рассмотреть использование ExpressRoute Direct?  

ExpressRoute Direct предоставляет клиентам пары прямых портов (100 Гбит/с) для подключения к глобальной магистральной сети Майкрософт. Клиенты могут получить наибольшие преимущества в следующих сценариях: интенсивный прием данных, физическая изоляция поднадзорных рынков и выделенная емкость в случае скачкообразной нагрузки (например, рендеринг). 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Какая модель выставление счетов используется для ExpressRoute Direct? 

Пара портов ExpressRoute Direct оплачивается по фиксированному тарифу. Каналы ценовой категории "Стандартный" будут предоставлены без дополнительных часов, а за каналы ценовой категории "Премиум" будет взиматься небольшая плата за надстройку. Исходящий трафик для каждого канала будет оплачиваться отдельно, в соответствии с зоной, в которой расположен пиринг.

### <a name="when-does-billing-state-for-the-expressroute-direct-port-pairs"></a>Когда выставляется счет для пар портов ExpressRoute Direct?

Пары портов ExpressRoute Direct оплачиваются через 45 дней после создания ресурса ExpressRoute Direct или при включении 1 или обеих ссылок, в зависимости от того, что наступит раньше. Предоставляется 45-дневный льготный период, чтобы клиенты могли завершить процесс перекрестного подключения с поставщиком расположения.
