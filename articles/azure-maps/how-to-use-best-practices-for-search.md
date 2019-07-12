---
title: Как выполнять поиск, эффективно с помощью службы поиска Azure Maps | Документация Майкрософт
description: Узнайте, как использовать рекомендации для поиска с помощью службы поиска Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8c4347eb0f89c17a285aaa4b51760300b9c89aa7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617864"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Советы и рекомендации для использования службы поиска Azure Maps

Azure Maps [службы поиска](https://docs.microsoft.com/rest/api/maps/search) включает API с помощью различных возможностей, например, из поиск адреса для поиска данных точки процент (стрелка) в указанной области. В этой статье мы будем публиковать рекомендации вызовов данные с помощью службы поиска Azure Maps. Вы научитесь:

* Создавать запросы, возвращающие связанные с ней соответствия
* Ограничение количества результатов поиска
* Узнайте, разница между различными типами результатов
* Чтение структуры ответа поиска адреса


## <a name="prerequisites"></a>предварительные требования

Чтобы выполнять вызовы к API службы "Карты", вам потребуется учетная запись и ключ службы "Карты". Дополнительные сведения о создании учетной записи и получении ключа см. в статье об [управлении ключами и учетной записью службы "Карты Azure"](how-to-manage-account-keys.md).

> [!Tip]
> Для запроса службы поиска, можно использовать [приложение Postman](https://www.getpostman.com/apps) для сборки REST вызовы, или же можно использовать любую среду разработки API, которое вы хотите использовать.


## <a name="best-practices-for-geocoding"></a>Советы и рекомендации для геокодирования

При поиске полной или частичной адрес, с помощью службы поиска Azure Maps, он принимает условия поиска и возвращает координаты широты и долготы адреса. Этот процесс называется геокодирования. Возможность геокодирования в стране зависит от данных о дорогах и точности геокодирования соответствующей службы.

См. в разделе [информации о геокодировании](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) узнать больше о возможностях геокодирования, службы карт Azure по странам и регионам.

### <a name="limit-search-results"></a>Ограничение количества результатов поиска

   В этом разделе вы узнаете, как использовать API-интерфейсы поиска "карты Azure" для ограничения результатов поиска. 

   > [!Note]
   > Не все API-интерфейсы, полностью поддерживают параметры, приведенные ниже

   **Результаты поиска Geo смещение**

   Чтобы geo смещение результатов для нужную область для пользователя, следует всегда добавлять максимально возможное число подробные входные данные о расположении. Чтобы ограничить результаты поиска, рассмотрите возможность добавления следующих входных типов:

   1. Задайте `countrySet` параметра, например «США, FR». Поиск по умолчанию задается для поиска по всему миру, ненужные результаты. Если запрос не содержит `countrySet` параметра поиска может возвращать неточные результаты. Например, поиск города с именем **Bellevue** будут выдаваться результаты из США и Франции, так как существуют городов под **Bellevue** во Франции и в США.

   2. Можно использовать `btmRight` и `topleft` поле параметров, чтобы задать ограничивающий прямоугольник для ограничения поиска в определенную область на карте.

   3. Чтобы повлиять на области релевантности результатов, можно определить `lat`и `lon` координации параметров и задайте области поиска с помощью радиус `radius` параметра.


   **Параметры поиска нечетких соответствий**

   1. `minFuzzyLevel` И `maxFuzzyLevel`, чтобы вернуть связанные с ней соответствия, даже в том случае, если параметры запроса не совсем соответствуют требуемой информации. Большинство запросов службы поиска по умолчанию для `minFuzzyLevel=1` и `maxFuzzyLevel=2` производительности и уменьшения количества необычных результатов. Рассмотрим в качестве примера искомого термина «restrant», она выделяется «ресторан», когда `maxFuzzyLevel` имеет значение 2. Уровни нечетких соответствий по умолчанию можно переопределить в соответствии с потребностями запроса. 

   2. Можно также указать точный набор типов результатов, возвращаемых с помощью `idxSet` параметра. Для этой цели вы можете отправить разделенный запятыми список индексов, порядок элементов не имеет значения. Ниже приведены поддерживаемые индексы.

       * `Addr` - **Диапазоны адресов**: Для некоторых улиц моментов адрес, на которые интерполируются в начале и конце улицы; Эти точки отображаются в виде диапазонов адресов.
       * `Geo` - **Географические области**: Области карты, представляющие административный Округ земли, то есть, страны, состояние, city.
       * `PAD` - **Адрес точки**:  Точек на карте, где определенный адрес, улица и номер можно найти в индексе, например, 2501 Soquel аварийного восстановления. Это самый высокий уровень точности для адреса.  
       * `POI` - **Достопримечательности**: Точек на карте, которые заслуживают внимания и может представлять интерес.  [Получить адрес поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) не вернет Геозоне.  
       * `Str` - **Streets**: Представление улиц на карте.
       * `XStr` - **CROSS улиц/пересечения**:  Представление соединения; места, где пересекаются два улицы.


       **Примеры использования**:

       * idxSet = POI (только в поиск точки Of Interest) 

       * idxSet = PAD, Addr (поиск только адреса PAD = адрес точки, Addr = диапазон адресов)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Обратный geocode» и «География фильтр типа сущности

При выполнении поиска с помощью обратного геокода [API обратного поиска адреса](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), у службы есть возможность возвращать многоугольников в области администрирования. Предоставляя параметр `entityType` в запросе, можно сузить область поиска типов сущностей указанного географического объекта. Полученный ответ будет содержать идентификатор географии, а также сопоставить тип сущности. Если имеется более одной сущности, конечная точка вернет **наименьшей сущностью доступных**. Возвращаемый идентификатор геометрии может использоваться для получения геометрию данном географическом регионе, через [службе получить многоугольника](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

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

`language` Параметра можно задать в какой язык поиска должны возвращаться результаты. Если в запросе не установлен язык, служба поиска автоматически по умолчанию самый распространенный язык в стране или регионе. Кроме того Если данные в указанный язык недоступен, используется язык по умолчанию. См. в разделе [поддерживаемые языки](https://docs.microsoft.com/azure/azure-maps/supported-languages) список поддерживаемых языков с точки зрения службы "карты Azure" по странам и регионам.


### <a name="predictive-mode-auto-suggest"></a>Прогнозной режим (автозаполнения)

Для поиска нескольких соответствий для частичных запросов, `typeahead` параметр должен быть установлен в значение «true». Запрос будет интерпретироваться как частичные входные данные и поиск перейдет в режим прогнозирования. В противном случае служба предполагает, что все важные сведения были переданы.

В примере запроса ниже можно см. в разделе, адрес службы поиска запрашиваются «Майкросо» с `typeahead` параметру присвоить **true**. Если вы заметили ответ, вы увидите, что служба поиска интерпретируется запроса в виде частичный запрос и ответ содержит результаты запроса, предложенные автоматически.

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


### <a name="uri-encoding-to-handle-special-characters"></a>URI, кодирования для обработки специальных символов 

Чтобы найти кросс-адреса, то есть «Avenue 1-го & Объединение Street, Сиэтл», специальный символ «&» должен быть закодирован перед отправкой запроса. Мы рекомендуем кодировать символьные данные в URI, там, где все символы кодируются с помощью знака «%» и шестнадцатеричное значение двух символов, соответствующий их символ UTF-8.

**Примеры использования**:

Получите адрес поиска:

```
query=1st Avenue & E 111th St, New York
```

 должен быть закодирован как:

```
query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


Ниже приведены различные способы для различных языков. 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/ VISUAL BASIC:
```C#
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

Ruby:
```Ruby
CGI::escape(query) 
```

SWIFT:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Перейти:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Советы и рекомендации для поиска ДОСТОПРИМЕЧАТЕЛЬНОСТЕЙ

Точки процент ДОСТОПРИМЕЧАТЕЛЬНОСТЯХ поиска позволяет запрашивать POI результаты по имени, например, поиск бизнеса по имени. Настоятельно рекомендуется использовать `countrySet` параметр для указания странах, где приложению требуется охватить, так как поиск по всему миру, ненужные результаты и/или к увеличению времени поиска будет поведение по умолчанию.

### <a name="brand-search"></a>Поиск торговой марки

Чтобы повысить Релевантность результатов и информацию в ответе, ответа на запросы поиска точки процент (стрелка) включает в себя сведения о торговых марках, который может использоваться дополнительно проанализировать ответ.

Давайте внесем [категории поиска ДОСТОПРИМЕЧАТЕЛЬНОСТЕЙ](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) запрос заправки практически кампусе Майкрософт (Редмонд, штат Вашингтон). Если вы заметили ответ, вы увидите сведения о торговых марках для каждая POI возвращается.

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


### <a name="airport-search"></a>Аэропорта поиска

Поиска ДОСТОПРИМЕЧАТЕЛЬНОСТЕЙ поддерживает поиск с помощью официальной коды аэропортов аэропортах. Например **SEA** (международный аэропорт Seattle Tacoma). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Рядом поиска

Для получения только POI результатов в указанной области [ближайших API поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) может оказаться правильным выбором. Эта конечная точка будет возвращать только результаты ДОСТОПРИМЕЧАТЕЛЬНОСТИ и не принимает в качестве параметра запроса поиска. Чтобы ограничить результаты, рекомендуется задать радиус.

## <a name="understanding-the-responses"></a>Основные сведения о ответы

Создадим запроса на поиск адреса для службы карт Azure [службы поиска](https://docs.microsoft.com/rest/api/maps/search) для адреса в Сиэтле. Если вы внимательно посмотреть на URL-АДРЕСЕ запроса ниже, мы задали `countrySet` параметр **США** для поиска адреса в США.

**Пример запроса**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Дополнительно давайте взглянем на структуру ответа ниже. Типы результата объектов результатов в ответе различны. Если вы заметили внимательно, можно увидеть, что у нас есть три различных типа объектов результатов, которые «Точки Address», «Street» и «Пересекающей улицы». Обратите внимание на то, что адрес поиска не возвращают Геозоне. `Score` Параметр для каждого объекта ответа указывает относительный показатель сопоставления для оценки других объектов в том же ответе. См. в разделе [получить адрес поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) Дополнительные сведения о параметров объекта ответа.

**Поддерживаемые типы результата:**

* **Адрес точки:** Точек на карте с определенным адресом с помощью улица и номер. Самый высокий уровень точности для адреса. 

* **Диапазон адресов:**  Для некоторых улиц моментов адрес, на которые интерполируются в начале и конце улицы; Эти точки отображаются в виде диапазонов адресов. 

* **География:** Области карты, представляющие административный Округ земли, то есть, страны, состояние, city. 

* **POI - (Достопримечательности):** Точек на карте, которые заслуживают внимания и может представлять интерес.

* **Улица:** Представление улиц на карте. Адреса сопоставляются координаты широты и долготы, содержащий адрес улицы. Номера домов могут не учитываться. 

* **Пересекающей улицы:** Пересечения. Представление соединения; места, где пересекаются два улицы.

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

### <a name="geometry"></a>Geometry

Если тип ответа — **Geometry**, он может включать идентификатор geometry, который возвращается в **источников данных** объекта, находящегося под «геометрия» и «id». Например [службе получить многоугольника](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) позволяет запрашивать геометрические данные в формате GeoJSON, например города или аэропорта структуры для набора сущностей. Можно использовать эти данные границ для [Geofencing](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) или [Геозоне поиска внутри геометрии](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


[Поиск адреса](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) или [поиска нечетких соответствий](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) ответов API может включать **идентификатор геометрии** , возвращается в объекте источников данных в разделе «геометрия» и «id».


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Следующие шаги

* Узнайте, [способы создания запросов службы поиска Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Изучите Azure Maps [документация по API службы поиска](https://docs.microsoft.com/rest/api/maps/search). 
