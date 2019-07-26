---
title: Эффективное Поиск с помощью службы поиска Azure Maps | Документация Майкрософт
description: Узнайте, как использовать рекомендации по поиску с помощью службы поиска Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 996a084fd653b2100d94313e8801d915b4bf2cf3
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348177"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Рекомендации по использованию Azure Maps Служба поиска

Azure Maps [Служба поиска](https://docs.microsoft.com/rest/api/maps/search) включает интерфейсы API с различными возможностями, например, от поиска адреса до поиска данных в точке интересов (достопримечательности), связанных с определенным расположением. В этой статье мы будем использовать рекомендации по вызову данных с помощью служб поиска Azure Maps. Вы научитесь:

* Построение запросов для возврата релевантных соответствий
* Ограничение результатов поиска
* Изучите разницу между различными типами результатов
* Чтение структуры ответа на поиск адреса


## <a name="prerequisites"></a>предварительные требования

Чтобы выполнять вызовы к API службы "Карты", вам потребуется учетная запись и ключ службы "Карты". Дополнительные сведения о создании учетной записи и получении ключа см. в статье об [управлении ключами и учетной записью службы "Карты Azure"](how-to-manage-account-keys.md).

> [!Tip]
> Чтобы выполнить запрос к службе поиска, можно использовать [приложение posts](https://www.getpostman.com/apps) для создания вызовов RESTful или использовать любую предпочтительную среду разработки API.


## <a name="best-practices-for-geocoding"></a>Рекомендации по геокодированию

При поиске полного или частичного адреса с помощью Azure Maps Служба поиска он принимает условия поиска и возвращает координаты адреса долготы и широты. Этот процесс называется геокодированием. Возможность геокодирования в стране зависит от данных о дорогах и точности геокодирования соответствующей службы.

Дополнительные сведения о Azure Maps возможности геокодирования по странам/регионам см. в разделе [покрытие геокодирования](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) .

### <a name="limit-search-results"></a>Ограничение результатов поиска

   В этом разделе вы узнаете, как использовать API Azure Maps поиска для ограничения результатов поиска. 

   > [!Note]
   > Не все API поиска полностью поддерживают указанные ниже параметры

   **Результаты поиска геосмещений**

   Чтобы геосдвигировать результаты в соответствующую область для пользователя, всегда следует добавлять максимально возможное подробное расположение. Чтобы ограничить результаты поиска, рассмотрите возможность добавления следующих входных типов:

   1. `countrySet` Задайте параметр, например "US, FR". Поведение поиска по умолчанию заключается в поиске по всему миру, потенциально возвращая ненужные результаты. Если запрос не содержит `countrySet` параметр, поиск может вернуть неточные результаты. Например, Поиск города с именем **Бельвью** возвратит результаты из США и Франции, так как в Франции и в США есть города с именем **Бельвью** .

   2. С помощью `btmRight` параметров и `topleft` можно задать ограничивающий прямоугольник, чтобы ограничить поиск определенной областью на карте.

   3. Чтобы повлиять на область релевантности результатов, можно определить `lat`параметры координат и `lon` и задать радиус области поиска с помощью `radius` параметра.


   **Параметры нечеткого поиска**

   1. `minFuzzyLevel` И`maxFuzzyLevel`помогают возвращать соответствующие соответствия даже в том случае, если параметры запроса не соответствуют нужной информации. Большинство запросов поиска по умолчанию `maxFuzzyLevel=2` имеет `minFuzzyLevel=1` значение и для повышения производительности и уменьшения необычных результатов. Возьмем пример условия поиска "рестрант", оно соответствует "ресторану", когда `maxFuzzyLevel` значение равно 2. По умолчанию нечеткие уровни могут быть переопределены в соответствии с потребностями запроса. 

   2. Можно также указать точный набор типов результатов, возвращаемых с помощью `idxSet` параметра. Для этой цели можно отправить список индексов с разделителями-запятыми, порядок элементов не имеет значения. Ниже приведены поддерживаемые индексы.

       * `Addr` - **Диапазоны адресов**: Для некоторых улиц есть точки с адресами, которые интерполируются с начала и с конца улицы; Эти точки представлены в виде диапазонов адресов.
       * `Geo` - **Географические**регионы: Области на карте, представляющие административное деление земли, то есть страна, штат, город.
       * `PAD` - **Адрес точки**:  Указывает на карту, где в индексе можно найти конкретный адрес с названием улицы и номером, например Сокуел Dr 2501. Это самый высокий уровень точности, доступный для адресов.  
       * `POI` - **Интересующие точки**: Баллы на карте, которые стоит обратить внимание и могут представлять интерес.  [Получение адреса поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) не возвращает интереса.  
       * `Str` - **Улиц**: Представление улиц на карте.
       * `XStr` - **Перекрестный/пересечение**:  Представление соединений; места, где два пересечения улиц.


       **Примеры использования**:

       * Идкссет = достопримечательности (только точки поиска по интересу) 

       * Идкссет = PAD, addr (только поиск адресов, PAD = адрес точки, addr = диапазон адресов)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Фильтровать тип сущности "геокод" и "географическая"

При выполнении обратного поиска по геокодированию с помощью [обратного API адреса поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)служба имеет возможность возвращать многоугольники для административных областей. Предоставляя параметр `entityType` в запросе, можно уменьшить поиск указанных типов сущностей geography. Полученный ответ будет содержать идентификатор географии, а также соответствующий тип сущности. Если вы предоставляете несколько сущностей, конечная точка возвратит **наименьшую доступную сущность**. Возвращенный идентификатор геометрии можно использовать для получения геометрии географического объекта через [службу получения многоугольников](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Пример запроса:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Ответ.**

```JSON
{
    "summary": {
        "queryTime": 8,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="search-results-language"></a>Язык результатов поиска

`language` Параметр позволяет указать, в каких языках должны возвращаться результаты поиска. Если язык не задан в запросе, служба поиска автоматически по умолчанию будет иметь наиболее распространенный язык в стране или регионе. Кроме того, если данные на указанном языке недоступны, используется язык по умолчанию. Список поддерживаемых языков см. в разделе [Поддерживаемые языки](https://docs.microsoft.com/azure/azure-maps/supported-languages) в отношении Azure Mapsных служб по странам или регионам.


### <a name="predictive-mode-auto-suggest"></a>Прогнозирующий режим (Auto-предлагаю)

Чтобы найти другие совпадения для частичных запросов `typeahead` , параметр должен иметь значение true. Запрос будет интерпретирован как частичный вход, и поиск будет выполнен в прогнозном режиме. В противном случае в службе предполагается, что все соответствующие сведения переданы.

В примере запроса ниже можно увидеть, что служба адреса поиска запрашивает "Майкрософт" с параметром, `typeahead` имеющим значение **true**. Если вы видите ответ, можно увидеть, что служба поиска интерпретирует запрос как частичный запрос, а ответ содержит результаты для автоматического предложенного запроса.

**Пример запроса**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**Ответ.**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>Кодирование URI для управления специальными символами 

Чтобы найти перекрестные адреса, то есть "1-й дом & Union, Сиэтл", Специальный символ "&" необходимо закодировать перед отправкой запроса. Мы рекомендуем кодировать символьные данные в URI, где все символы кодируются с помощью символа "%" и шестнадцатеричного значения из двух символов, соответствующего символу UTF-8.

**Примеры использования**:

Получить адрес для поиска:

```
query=1st Avenue & E 111th St, New York
```

 должен быть закодирован как:

```
query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


Ниже приведены различные методы использования для разных языков. 

JavaScript и TypeScript:
```Javascript
encodeURIComponent(query)
```

C#VB
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP.
```PHP
urlencode(query)
```

Ruby
```Ruby
CGI::escape(query) 
```

SWIFT
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Попасть
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Рекомендации по достопримечательности поиску

Поиск по интересам (достопримечательности) позволяет запрашивать результаты достопримечательности по имени, например, искать бизнес по имени. Мы настоятельно рекомендуем использовать `countrySet` параметр, чтобы указать страны, в которых приложение нуждается в покрытии, так как поведение по умолчанию будет искать весь мир, потенциально возвращая ненужные результаты и/или выполняя более длительное время поиска.

### <a name="brand-search"></a>Поиск фирменной символики

Для повышения релевантности результатов и информации в ответе на запрос поиска в ответе (достопримечательности) включаются сведения о торговой марке, которые можно использовать для анализа ответа.

Давайте создадим запрос [поиска по категории Достопримечательности](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) для станций газа рядом с Microsoft кампуса (Redmond, WA). Если вы видите ответ, то можете увидеть сведения о торговой марке для каждого возвращенного достопримечательности.

**Пример запроса**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**Ответ.**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "url": "www.chevron.com",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        ...,
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "url": "www.texaco.com/",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Поиск в аэропорту

Поиск достопримечательности поддерживает поиск в аэропортах с помощью официальных кодов аэропорта. Например, **Sea** (Сиэтл-Tacoma Международный аэропорт). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Поиск поблизости

Чтобы получить только результаты достопримечательности в определенном расположении, можно выбрать [ближайший API поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) . Эта конечная точка будет возвращать только результаты достопримечательности и не принимает параметр поискового запроса. Чтобы ограничить результаты, рекомендуется задать радиус.

## <a name="understanding-the-responses"></a>Основные сведения о ответах

Давайте создадим запрос на поиск адреса в [службе поиска](https://docs.microsoft.com/rest/api/maps/search) Azure Maps для адреса в Сиэтле. Если вы внимательно взгляните на URL-адрес запроса, приведенный ниже `countrySet` , мы придали параметру значение **US** для поиска адреса в США America.

**Пример запроса**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Рассмотрим приведенную ниже структуру ответа. Результирующие типы результирующих объектов в ответе различаются. При внимательном рассмотрении можно увидеть, что у нас есть три различных типа результирующих объектов: "адрес точки", "улица" и "перекрестный дом". Обратите внимание, что поиск адреса не возвращает интереса. `Score` Параметр для каждого объекта ответа указывает относительный показатель сопоставления с результатами других объектов в том же ответе. Дополнительные сведения о параметрах объекта Response см. в разделе [Получение адреса поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) .

**Поддерживаемые типы результатов:**

* **Адрес точки:** Указывает на карту с указанным адресом, названием и номером улицы. Максимальный уровень точности, доступный для адресов. 

* **Диапазон адресов:**  Для некоторых улиц есть точки с адресами, которые интерполируются с начала и с конца улицы; Эти точки представлены в виде диапазонов адресов. 

* **Geography** Области на карте, представляющие административное деление земли, то есть страна, штат, город. 

* **ДОСТОПРИМЕЧАТЕЛЬНОСТИ — (интересующие точки):** Баллы на карте, которые стоит обратить внимание и могут представлять интерес.

* **Улице** Представление улиц на карте. Адреса разрешаются в координаты широты и долготы улицы, содержащей адрес. Номера домов могут не учитываться. 

* **Перекрестная Улица:** Пересечения. Представления соединений; места, где два пересечения улиц.

**Ответ.**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Геометрия

Если тип отклика — **Geometry**, он может содержать идентификатор геометрии, возвращаемый в объекте **DataSources** в области "Geometry" и "ID". Например, [Служба получения многоугольников](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) позволяет запрашивать геометрические данные в формате геоjson, например в городе или в аэропорту для набора сущностей. Эти граничные данные можно использовать для [геозоны](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) или [поиска интереса внутри геометрии](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


[Адрес поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) или ответы API нечеткого [поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) могут содержать **идентификатор геометрии** , возвращаемый в объекте DataSources в области "Geometry" и "ID".


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Следующие шаги

* Узнайте [, как создавать запросы к службе поиска Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Изучите [документацию по API службы поиска](https://docs.microsoft.com/rest/api/maps/search)Azure Maps. 
