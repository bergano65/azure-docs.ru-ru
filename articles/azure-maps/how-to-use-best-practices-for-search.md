---
title: Рекомендации для службы поиска карт Azure (ru) Карты Microsoft Azure
description: Узнайте, как применять лучшие практики при использовании службы поиска с помощью Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8d62d7d278323baa0ae49b9e12f46468efb067a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335307"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Рекомендации для службы поиска карт Azure

[Служба поиска](https://docs.microsoft.com/rest/api/maps/search) Карт Azure включает в себя API, которые предлагают различные возможности. Например, API поиска адреса может найти точки интереса (POI) или данные вокруг определенного местоположения. 

В этой статье объясняется, как применять звуковые практики при вызове данных из службы поиска Azure Maps. Вы узнаете, как:

* Создавайте запросы для возврата соответствующих совпадений.
* Ограничьте результаты поиска.
* Узнайте различия между типами результатов.
* Прочитайте структуру поиска-ответа адреса.

## <a name="prerequisites"></a>Предварительные требования

Для вызова aPI службы Azure Maps необходимо учетную запись Azure Maps и ключ. Для получения дополнительной информации [см. Создать учетную запись](quick-demo-map-app.md#create-an-account-with-azure-maps) и получить [основной ключ](quick-demo-map-app.md#get-the-primary-key-for-your-account). 

Для получения информации о проверке подлинности в Azure Maps [см.](./how-to-manage-authentication.md)

> [!TIP]
> Чтобы заставить службу поиска запроса, вы можете использовать [приложение Почтальон](https://www.getpostman.com/apps) для создания вызовов REST. Или вы можете использовать любую среду разработки API, которая вам предпочтет.

## <a name="best-practices-to-geocode-addresses"></a>Рекомендации по геокодовым адресам

При поиске полного или частичного адреса с помощью службы поиска Azure Maps API считывает ключевые слова из поискового запроса. Затем он возвращает долготу и координаты широты адреса. Этот процесс называется *геокодированием.* 

Способность к геокодированию в стране зависит от наличия дорожных данных и точности службы геокодирования. Для получения дополнительной информации о возможностях геокодирования Azure Maps по странам или регионам можно ознакомиться [с обзором геокодирования.](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage)

### <a name="limit-search-results"></a>Ограничьте результаты поиска

 API поиска Карт Azure может помочь вам надлежащим образом ограничить результаты поиска. Вы ограничиваете результаты, чтобы можно было отображать соответствующие данные своим пользователям.

> [!NOTE]
> AIS поиска поддерживают больше параметров, чем просто те, которые обсуждается в этой статье.

#### <a name="geobiased-search-results"></a>Геопредвзятые результаты поиска

Чтобы геобиас результаты в соответствующую область для пользователя, всегда добавляйте как можно больше деталей местоположения, как это возможно. Возможно, необходимо ограничить результаты поиска, указав некоторые типы ввода:

* Установите `countrySet` параметр. Вы можете установить `US,FR`его, например. По умолчанию API выполняет поиск по всему миру, поэтому он может вернуть ненужные результаты. Если ваш запрос `countrySet` не имеет параметра, то поиск может вернуть неточные результаты. Например, поиск города под названием *Bellevue* возвращает результаты из США и Франции, потому что обе страны содержат город под названием *Bellevue*.

* Вы можете `btmRight` использовать `topleft` и параметры для установки ограничивающего окна. Эти параметры ограничивают поиск определенной областью на карте.

* Чтобы повлиять на область, релевантную для результатов, определите `lat` и `lon` координируете параметры. Используйте `radius` параметр для установки радиуса области поиска.


#### <a name="fuzzy-search-parameters"></a>Параметры нечеткого поиска

Мы рекомендуем использовать [API поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) Azure Maps, если вы не знаете, что пользователи вносит в запрос поиска. API сочетает в себе поиск POI и геокодирование в канонический *однолинейный поиск:* 

* Параметры `minFuzzyLevel` `maxFuzzyLevel` и параметры помогают вернуть соответствующие совпадения, даже если параметры запроса точно не соответствуют информации, которую пользователь хочет. Чтобы максимизировать производительность и уменьшить необычные результаты, установите поисковые запросы по умолчанию `minFuzzyLevel=1` и `maxFuzzyLevel=2`. 

    Например, когда `maxFuzzyLevel` параметр установлен до 2, термин поиска *подогнан* *рестораном.* Вы можете переопределить нечеткие уровни по умолчанию, когда это необходимо. 

* Используйте `idxSet` параметр, чтобы определить точный набор типов результатов. Чтобы определить точный набор результатов, можно отправить список индексов, разделенных запятой. В вашем списке заказ товара не имеет значения. Azure Maps поддерживает следующие индексы:

* `Addr` - **Диапазоны адресов**: Адресные точки, интерполированные от начала и до конца улицы. Эти точки представлены как адресные диапазоны.
* `Geo` - **География**: Административные деления земли. География может быть, например, страной, штатом или городом.
* `PAD` - **Адреса точек**: Адреса, включающие название улицы и номер. Адреса точек можно найти в индексе. Примером является *Soquel Dr 2501*. Адрес точки обеспечивает наивысший уровень точности, доступный для адресов.  
* `POI` - **Точки интереса**: Очки на карте, которые считаются достойными внимания или которые могут быть интересными. [API адреса поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) не возвращает POIs.  
* `Str` - **Улицы**: Улицы на карте.
* `XStr` - **Пересекай улицы или перекрестки:** развязки или места, где пересекаются две улицы.


#### <a name="usage-examples"></a>Примеры использования

* `idxSet=POI`- Поиск poIs только. 

* `idxSet=PAD,Addr`- Поиск адреса только. `PAD`указывает адрес точки `Addr` и указывает диапазон адресов.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Обратный геокод и фильтр для типа географического объекта

При поиске обратного геокода в [Search Address Reverse API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)служба может возвращать полигоны для административных областей.Чтобы сузить поиск до определенных типов сущностей географии, включите `entityType` параметр в свои запросы. 

Полученный ответ содержит идентификатор географии и тип сущности, который был сопоставлен. Если вы предоставляете несколько объектов, то конечная точка возвращает *наименьшую доступенную сущность.* Вы можете использовать идентификатор возвращенной геометрии, чтобы получить геометрию географии через [службу Поиска Полигона.](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)

#### <a name="sample-request"></a>Пример запроса

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>Ответ

```JSON
{
    "summary": {
        "queryTime": 14,
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

### <a name="set-the-results-language"></a>Настройка языка результатов

Используйте `language` параметр для настройки языка для вернувшихся результатов поиска. Если запрос не устанавливает язык, то по умолчанию Служба поиска использует наиболее распространенный язык в стране или регионе. Когда данные не доступны на указанном языке, используется язык по умолчанию. 

Для получения дополнительной информации смотрите [поддерживаемые языки Azure Maps.](https://docs.microsoft.com/azure/azure-maps/supported-languages)


### <a name="use-predictive-mode-automatic-suggestions"></a>Использование прогностического режима (автоматические предложения)

Чтобы найти больше совпадений `typeahead` для `true`частичных запросов, установите параметр. Этот запрос интерпретируется как частичный вход, и поиск входит в режим прогнозирования. Если вы не установите `typeahead` `true`параметр, то служба предполагает, что вся соответствующая информация была передана дюйма

В следующем запросе примера служба поиска адреса запрашивается для *Microso*. Здесь `typeahead` параметр установлен `true`на . Ответ показывает, что служба поиска интерпретировала запрос как частичный запрос. Ответ содержит результаты для автоматического предложенного запроса.

#### <a name="sample-query"></a>Пример запроса

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>Ответ

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="encode-a-uri-to-handle-special-characters"></a>Закодировать URI для обработки специальных символов 

Чтобы найти адреса перекрестка, необходимо кодировать URI для обработки специальных символов в адресе. Рассмотрим этот адресный пример: *1-я авеню & Union Street, Сиэтл*. Здесь, кодировать амперсанд характер ()`&`перед отправкой запроса. 

Мы рекомендуем кодировать данные символов в URI. В URI вы кодируете все символы,`%`используя знак процента () и гексадецимельное значение двух символов, которое соответствует коду UTF-8 символов.

#### <a name="usage-examples"></a>Примеры использования

Начните с этого адреса:

```
query=1st Avenue & E 111th St, New York
```

Кодирование адреса:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

Вы можете использовать следующие методы.

JavaScript или TypeScript:
```Javascript
encodeURIComponent(query)
```

C# или Visual Basic: 
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

Руби:
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Пойти:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>Рекомендации по поиску POI

При поиске POI вы можете запросить результаты POI по имени. Например, можно искать бизнес по имени. 

Мы настоятельно рекомендуем вам `countrySet` использовать этот параметр для указания стран, в которых ваша заявка нуждается в страховом покрытии. Поведение по умолчанию заключается в поиске по всему миру. Этот широкий поиск может вернуть ненужные результаты, и поиск может занять много времени.

### <a name="brand-search"></a>Поиск бренда

Для повышения актуальности результатов и информации в ответе, ответ поиска POI включает информацию о бренде. Вы можете использовать эту информацию для дальнейшего изучения ответа.

В запросе вы можете отправить запятую список торговых марок. Используйте список, чтобы ограничить результаты `brandSet` конкретными брендами, установив параметр. В вашем списке заказ номенклатуры не имеет значения. Когда вы предоставляете несколько списков брендов, результаты, которые возвращаются должны принадлежать по крайней мере один из ваших списков.

Чтобы изучить поиск бренда, давайте сделаем запрос [на поиск категории POI.](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) В следующем примере мы ищем заправочные станции рядом с кампусом Microsoft в Редмонде, штат Вашингтон. Ответ показывает информацию о бренде для каждого возвращенного POI.

#### <a name="sample-query"></a>Пример запроса

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>Ответ

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
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
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Поиск в аэропорту

Используя API поиска POI, вы можете искать аэропорты, используя их официальный код. Например, можно использовать *SEA* для поиска международного аэропорта Сиэтл-Такома: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Поиск поблизости

Чтобы получить результаты POI в определенном месте, вы можете попробовать использовать [API поиска.](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) Конечная точка возвращает только результаты POI. Он не принимает параметр поискового запроса. 

Чтобы ограничить результаты, мы рекомендуем установить радиус.

## <a name="understanding-the-responses"></a>Понимание ответов

Давайте найдем адрес в Сиэтле, направив запрос на поиск адресов в службу поиска Azure Maps. В следующем URL-адресе `countrySet` мы `US` замыкаем параметр для поиска адреса в США.

### <a name="sample-query"></a>Пример запроса

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Поддерживаемые типы результатов

* **Адрес точки**: Очки на карте, которые имеют определенный адрес с именем улицы и номером. Адрес point обеспечивает наивысший уровень точности адресов. 

* **Диапазон адресов**: Диапазон адресных точек, интерполированных от начала и до конца улицы.  

* **География**: Области на карте, которые представляют административные разделения земли, например, страны, штата или города. 

* **POI**: Очки на карте, которые заслуживают внимания, и что может быть интересно.

* **Улица**: Улицы на карте. Адреса решаются на широту и долготы координаты улицы, которая содержит адрес. Номер дома может не быть обработан. 

* **Крест-стрит**: Пересечения. Крестные улицы представляют собой развязки, где пересекаются две улицы.

### <a name="response"></a>Ответ

Давайте посмотрим на структуру ответа. В следующем ответе типы объектов результата различны. Если вы посмотрите внимательно, вы увидите три типа объектов результата:

* Адрес точки
* Улица
* Кросс-стрит

Обратите внимание, что поиск адреса не возвращает POIs.  

Параметр `Score` для каждого объекта ответа показывает, как соответствующая оценка соотносится со счетами других объектов в том же ответе. Для получения дополнительной информации о [Get Search Address](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)параметрах объекта ответа см.

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometry

Тип ответа *Geometry* может включать идентификатор геометрии, `geometry` `id`который возвращается в `dataSources` объект под и . Например, можно использовать [службу поиска Polygon](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) для запроса данных геометрии в формате GeoJSON. Используя этот формат, вы можете получить наброски города или аэропорта для набора сущностей. Затем можно использовать эти пограничные данные для [настройки геоограждения](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) или [поиска POIs внутри геометрии.](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)


Ответы на API [поиска адреса](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) или [API поиска нечеткой](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) может включать идентификатор геометрии, который возвращается в `dataSources` объекте под `geometry` и: `id`


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше, пожалуйста, см.:

> [!div class="nextstepaction"]
> [Как создать поисковую службу Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

> [!div class="nextstepaction"]
> [Документация API службы поиска](https://docs.microsoft.com/rest/api/maps/search)