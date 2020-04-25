---
title: Политики в службе управления API Azure | Документация Майкрософт
description: Сведения о политиках, доступных для использования в службе управления API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: f4a29c7cb7e35e69eb9410dd7ddc9f07757e3565
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82128717"
---
# <a name="api-management-policies"></a>Политики управления API
В этом разделе рассматриваются приведенные ниже политики управления API. Дополнительные сведения о добавлении и настройке политик см. в статье о [политиках в управлении API](api-management-howto-policies.md).  
  
 Политики представляют собой одну из эффективных функций системы, позволяющих издателю изменять поведение интерфейса API путем его настройки. Политика — это коллекция операторов, которые выполняются последовательно по запросу интерфейса API или при получении из него ответа. К часто используемым операторам относятся преобразование формата из XML в JSON, а также ограничение скорости вызовов, позволяющее ограничивать количество входящих вызовов от разработчика. Также существует много готовых политик.  
  
 Выражения политики можно использовать в качестве значений атрибутов или текстовых значений в любой политике управления API, если в ней не указано иное. Некоторые политики (в том числе [Поток управления](api-management-advanced-policies.md#choose) и [Задание переменной](api-management-advanced-policies.md#set-variable)) основаны на выражениях политики. Дополнительные сведения см. в разделе [Расширенные политики](api-management-advanced-policies.md#AdvancedPolicies) и [выражения политик](api-management-policy-expressions.md).  
  
##  <a name="policies"></a><a name="ProxyPolicies"></a>Политике  
  
-   [Политики ограничения доступа](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Проверка заголовка HTTP](api-management-access-restriction-policies.md#CheckHTTPHeader) – обеспечивает принудительный ввод заголовка HTTP и/или его значения.  
    -   [Ограничение частоты вызовов по подписке](api-management-access-restriction-policies.md#LimitCallRate) — предотвращает пики использования API, ограничивая частоту вызовов для каждой подписки.  
    -   [Ограничение частоты вызовов по ключу](api-management-access-restriction-policies.md#LimitCallRateByKey) — предотвращает пики использования API, ограничивая частоту вызовов по ключу.  
    -   [Ограничение IP-адресов вызывающих объектов](api-management-access-restriction-policies.md#RestrictCallerIPs) – фильтрует (разрешает или запрещает) вызовы с конкретных IP-адресов и/или диапазонов адресов.  
    -   [Задание квоты использования по подписке](api-management-access-restriction-policies.md#SetUsageQuota) — позволяет принудительно устанавливать возобновляемую или действующую в течение срока службы квоту на число вызовов и (или) квоту пропускной способности для каждой подписки.  
    -   [Задание квоты использования по ключу](api-management-access-restriction-policies.md#SetUsageQuotaByKey) — позволяет принудительно устанавливать возобновляемую или действующую в течение срока службы квоту на число вызовов и (или) квоту пропускной способности для каждого ключа.  
    -   [Проверка JWT](api-management-access-restriction-policies.md#ValidateJWT) – обеспечивает принудительное задание и проверку JWT, извлеченного из заданного заголовка HTTP или параметра запроса.  
-   [Расширенные политики](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Поток управления](api-management-advanced-policies.md#choose) — условно применяет правила политики на основе вычисления логических выражений.  
    -   [Перенаправляющий запрос](api-management-advanced-policies.md#ForwardRequest) — перенаправляет запрос в серверную службу.
    -   [Ограничения параллелизма](api-management-advanced-policies.md#LimitConcurrency) не позволяет, чтобы заключенные политики одновременно выполнялись запросами, количество которых выше указанного.
    -   [Регистрация в концентраторе событий](api-management-advanced-policies.md#log-to-eventhub) — отправляет сообщения в определенном формате объекту, который указан сущностью Logger.
    -   [Макетирование ответа](api-management-advanced-policies.md#mock-response) — прекращает выполнение конвейера и возвращает макетированный ответ непосредственно вызывающему объекту.
    -   [Повторить](api-management-advanced-policies.md#Retry) — повторяет выполнение инструкций встраиваемой политики, если не выполнено условие и до тех пор пока оно не будет выполнено. Выполнение будет повторяться через определенные промежутки времени и до указанного количества повторных попыток.  
    -   [Возврат ответа](api-management-advanced-policies.md#ReturnResponse) — прекращает выполнение конвейера и возвращает указанный ответ непосредственно вызывающему объекту.  
    -   [Отправка одностороннего запроса](api-management-advanced-policies.md#SendOneWayRequest) — отправляет запрос на указанный URL-адрес и не ожидает ответа.  
    -   [Отправка запроса](api-management-advanced-policies.md#SendRequest) — отправляет запрос на указанный URL-адрес.
    -   [Установка прокси-сервера HTTP](api-management-advanced-policies.md#SetHttpProxy) — позволяет маршрутизировать перенаправленные запросы через прокси-сервер HTTP.
    -   [Установка переменной](api-management-advanced-policies.md#set-variable) — сохраняет значение в именованной переменной контекста для последующего использования.  
    -   [Установка метода запроса](api-management-advanced-policies.md#SetRequestMethod) — позволяет изменить метод HTTP для запроса.  
    -   [Установка кода состояния](api-management-advanced-policies.md#SetStatus) — меняет код состояния HTTP на указанное значение.  
    -   [Трассировка](api-management-advanced-policies.md#Trace) . Добавляет пользовательские трассировки в выходные данные [инспектора API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) , Application Insights телеметрии и журналы ресурсов.  
    -   [Ожидание](api-management-advanced-policies.md#Wait) — ожидание вложенных [запросов на отправку](api-management-advanced-policies.md#SendRequest), [Получение значения из кэша](api-management-caching-policies.md#GetFromCacheByKey)или политики [потока управления](api-management-advanced-policies.md#choose) для завершения перед продолжением.  
-   [Политики проверки подлинности](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Обычная проверка подлинности](api-management-authentication-policies.md#Basic) – обычная проверка подлинности внутренней службы.  
    -   [Аутентификация с помощью сертификата клиента](api-management-authentication-policies.md#ClientCertificate) – аутентификация внутренней службы с помощью сертификатов клиентов.  
    -   [Аутентификация с помощью управляемого удостоверения](api-management-authentication-policies.md#ManagedIdentity) — аутентификация в серверной службе с помощью [управляемого удостоверения](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
-   [Политики кэширования](api-management-caching-policies.md#CachingPolicies)  
    -   [Получение из кэша](api-management-caching-policies.md#GetFromCache) – выполнение операции поиска в кэше и возврат допустимого кэшированного ответа при его наличии.  
    -   [Сохранение в кэше](api-management-caching-policies.md#StoreToCache) – помещение в кэш ответа в соответствии с заданной конфигурацией управления кэшем.  
    -   [Получение значения из кэша](api-management-caching-policies.md#GetFromCacheByKey) — получение кэшированного элемента по ключу.  
    -   [Сохранение значения в кэше](api-management-caching-policies.md#StoreToCacheByKey) — сохранение элемента в кэше по ключу.  
    -   [Удалить значение из кэша](api-management-caching-policies.md#RemoveCacheByKey) — удаление элемента в кэше по ключу.  
-   [Кроссдоменные политики](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Разрешение кросс-доменных вызовов](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – делает API доступным из клиентов на основе браузеров Adobe Flash и Microsoft Silverlight.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) – добавляет поддержку общего доступа к ресурсам независимо от источника (CORS) для операции или API, чтобы разрешить кросс-доменные вызовы от клиентов на основе браузера.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) – добавляет поддержку JSON с заполнением (JSONP) для операции или API, чтобы разрешить кросс-доменные вызовы из браузерных клиентов JavaScript.  
-   [Политики преобразования](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Преобразование JSON в XML](api-management-transformation-policies.md#ConvertJSONtoXML) — преобразует текст запроса или ответа в формате JSON в формат XML.  
    -   [Преобразование XML в JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) — преобразует текст запроса или ответа в формате XML в формат JSON.  
    -   [Поиск и замена строки в тексте](api-management-transformation-policies.md#Findandreplacestringinbody) — позволяет найти подстроку запроса или ответа и заменить ее на другую подстроку.  
    -   [Маскировка URL-адресов в содержимом](api-management-transformation-policies.md#MaskURLSContent) — перезаписывает (маскирует) ссылки в тексте ответа так, чтобы каждая из них указывала на эквивалентную ссылку через шлюз.  
    -   [Задание внутренней службы](api-management-transformation-policies.md#SetBackendService) – изменяет внутреннюю службу для входящего запроса.  
    -   [Задание текста](api-management-transformation-policies.md#SetBody) – задает текст сообщения для входящих и исходящих запросов.  
    -   [Установка HTTP-заголовка](api-management-transformation-policies.md#SetHTTPheader) -– назначает значение существующему заголовку ответа и/или запроса или добавляет новый заголовок ответа и/или запроса.  
    -   [Настройка параметра строки запроса](api-management-transformation-policies.md#SetQueryStringParameter) - добавляет, заменяет значение или удаляет параметр строки запроса.  
    -   [Перезапись URL-адреса](api-management-transformation-policies.md#RewriteURL) — преобразовывает URL-адрес запроса из его общедоступной формы в форму, ожидаемую веб-службой.  
    -   [Преобразование XML с помощью XSLT](api-management-transformation-policies.md#XSLTransform) — применяет преобразование данных в формате XSL в формат XML в тексте запроса или ответа.  



## <a name="next-steps"></a>Следующие шаги
Дополнительные сведения о работе с политиками см. в следующих статьях:

+ [Политики в управлении API](api-management-howto-policies.md)
+ [Преобразование API-интерфейсов](transform-api.md).
+ [Примеры политик](policy-samples.md).   
