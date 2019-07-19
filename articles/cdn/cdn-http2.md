---
title: Поддержка HTTP/2 в Azure CDN | Документы Майкрософт
description: Сведения о поддержке HTTP/2 и CDN.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: ril
ms.openlocfilehash: 703623e3f7f314d87417458f3f9a218dfdf45427
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849968"
---
# <a name="http2-support-in-azure-cdn"></a>Поддержка HTTP/2 в Azure CDN

HTTP/2 является основной редакцией HTTP/1.1\. Этот протокол обеспечивает более высокую производительность, сокращение времени ответа и более удобную среду работы пользователей наряду с привычными методами, кодами состояния и семантикой HTTP. Хотя HTTP/2 предназначен для работы с HTTP и HTTPS, многие клиентские веб-браузеры поддерживают только HTTP/2 через TLS.

### <a name="http2-benefits"></a>Преимущества HTTP/2

Преимущества HTTP/2 перечислены ниже.

*   **Мультиплексирование и параллелизм**

    При использовании HTTP 1.1 для выполнения нескольких запросов к ресурсам требуется несколько TCP-подключений, и с каждым подключением связаны определенные затраты на обеспечение производительности. HTTP/2 позволяет запрашивать несколько ресурсов через одно TCP-подключение.

*   **Сжатие заголовка**

    Благодаря сжатию HTTP-заголовков для обслуживаемых ресурсов значительно уменьшается время в сети.

*   **Зависимости потоков**

    Зависимости потоков позволяют клиенту указывать серверу, какие ресурсы имеют приоритет.


## <a name="http2-browser-support"></a>Поддержка HTTP/2 в браузерах

Во всех текущих версиях основных браузеров реализована поддержка HTTP/2. Неподдерживаемые браузеры будет автоматически использовать HTTP/1.1.

|Browser|Минимальная версия|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Включение HTTP/2 в Azure CDN

Сейчас HTTP/2 поддерживается во всех профилях Azure CDN. Никаких действий со стороны пользователей не требуется.

## <a name="next-steps"></a>Следующие шаги

Преимущества HTTP/2 в действии представлены в [этой демонстрации от Akamai](https://http2.akamai.com/demo).

Дополнительные сведения о HTTP/2 см. в следующих ресурсах.

*   [Домашняя страница спецификации HTTP/2](https://http2.github.io/)
*   [Официальная страница часто задаваемых вопросов об HTTP/2](https://http2.github.io/faq/)
*   [Сведения об HTTP/2 на сайте Akamai](https://http2.akamai.com/)

Дополнительные сведения о доступных функциях Azure CDN см. в статье [Общие сведения о сети доставки содержимого (CDN) Azure](https://azure.microsoft.com/documentation/articles/cdn-overview/).