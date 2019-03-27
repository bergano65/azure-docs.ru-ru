---
title: Сравнение функций продуктов сети доставки содержимого Azure (CDN) | Документация Майкрософт
description: Сведения о возможностях, которые поддерживает каждый продукт Azure доставки содержимого сети (CDN).
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/28/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: cce28b031b146d8a56d37647022261294f07f0be
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57213574"
---
# <a name="compare-azure-cdn-product-features"></a>Сравнение функций продуктов Azure CDN

Сеть доставки содержимого Microsoft Azure (CDN) включает в себя четыре продукта: **Azure CDN уровня "Стандартный" от корпорации Майкрософт**, **Azure CDN уровня "Стандартный" от Akamai**, **Azure CDN уровня "Стандартный" от Verizon** и **Azure CDN уровня "Премиум" от Verizon**. Дополнительные сведения о переходе с профиля **Azure CDN ценовой категории "Стандартный" от Verizon** на профиль **Azure CDN ценовой категории "Премиум" от Verizon** см. [здесь](cdn-migrate.md).

В следующей таблице перечислены функции, доступные в каждом из продуктов.

| **Возможности для повышения производительности и оптимизации** | **Уровень "Стандартный" от корпорации Майкрософт** | **Akamai уровня "Стандартный"** | **Verizon уровня "Стандартный"** | **Verizon уровня "Премиум"** |
| --- | --- | --- | --- | --- |
| [Динамическое ускорение сайтов](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  |  | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Динамическое ускорение сайтов — адаптивное сжатие изображений](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Динамическое ускорение сайтов — предварительная выборка объектов](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Оптимизация общей веб-доставки](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;**. Этот тип оптимизации используется для файлов со средним размером не более 10 МБ.  | **&#x2713;** |  **&#x2713;** |
| [Оптимизация потоковой передачи видео](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | с помощью общей веб-доставки | **&#x2713;**  | с помощью общей веб-доставки |  с помощью общей веб-доставки |
| [Оптимизация больших файлов](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | с помощью общей веб-доставки | **&#x2713;**. Этот тип оптимизации используется для файлов размером более 10 МБ.   | с помощью общей веб-доставки |  с помощью общей веб-доставки |
| Изменение типа оптимизации | |**&#x2713;** | | |
| Порт источника |Все TCP-порты |[Разрешенные порты источника](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Все TCP-порты |Все TCP-порты |
| [Глобальная балансировка нагрузки сервера (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Быстрая очистка.](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**. "Очистить все" и "Очистка по подстановочным знакам" сейчас не поддерживается в Azure CDN от Akamai. |**&#x2713;** |**&#x2713;** |
| [Предварительная загрузка ресурса.](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Параметры кэша и заголовка (с использованием [правил кэширования](cdn-caching-rules.md))  |  |**&#x2713;** |**&#x2713;** | |
| Настраиваемый модуль доставки контента на основе правил (с помощью [обработчика правил](cdn-rules-engine.md)).  |  | | |**&#x2713;** |
| Параметры кэша и заголовка (с использованием [обработчика правил](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Перенаправление или перезапись URL-адреса (с использованием [обработчика правил](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Правила для мобильных устройств (с использованием [обработчик правил](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| [Кэширование строк запроса.](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Двойной стек IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Поддержка HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Безопасность** | **Уровень "Стандартный" от корпорации Майкрософт** | **Akamai уровня "Стандартный"** | **Verizon уровня "Стандартный"** | **Verizon уровня "Премиум"** | 
| Поддержка HTTPS с конечной точкой CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Пользовательский домен HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Поддержка пользовательских доменных имен.](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Геофильтрация](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Проверка подлинности по маркерам](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Защита от атак DDoS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Использование собственного сертификата](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  |  |  |
||||
| **Аналитика и отчетность** | **Уровень "Стандартный" от корпорации Майкрософт** | **Akamai уровня "Стандартный"** | **Verizon уровня "Стандартный"** | **Verizon уровня "Премиум"** | 
| [Журналы диагностики Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Основные отчеты из Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Настраиваемые отчеты из Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Расширенные HTTP-отчеты.](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Статистика в режиме реального времени.](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Производительность граничного узла](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Оповещения в реальном времени](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Простота использования** | **Уровень "Стандартный" от корпорации Майкрософт** | **Akamai уровня "Стандартный"** | **Verizon уровня "Стандартный"** | **Verizon уровня "Премиум"** | 
| Простая интеграция со службами Azure, такими как [служба хранилища](cdn-create-a-storage-account-with-cdn.md), [веб-приложения](cdn-add-to-web-app.md) и [Службы мультимедиа](../media-services/media-services-portal-manage-streaming-endpoints.md).  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Управление с помощью [REST API](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) или [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Типы сжатия MIME](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Только по умолчанию |Настраивается |Настраивается  |Настраивается  |
| Кодирование сжатия  |gzip, brotli |gzip |gzip, deflate, bzip2, brotili  |gzip, deflate, bzip2, brotili  |






