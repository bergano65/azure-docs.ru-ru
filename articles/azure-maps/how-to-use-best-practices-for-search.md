---
title: Рекомендации для Azure Maps Служба поиска | Карты Microsoft Azure
description: Узнайте, как применять рекомендации при использовании Служба поиска из карт Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8d62d7d278323baa0ae49b9e12f46468efb067a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335307"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Рекомендации по Azure Maps Служба поиска

Azure Maps [Служба поиска](https://docs.microsoft.com/rest/api/maps/search) содержит интерфейсы API, которые предлагают различные возможности. Например, API поиска адреса может находить интересующие вас точки (достопримечательности) или данные, относящиеся к определенному расположению. 

В этой статье объясняется, как применять методики звука при вызове данных из Azure Maps Служба поиска. Вы узнаете, как:

* Создание запросов для возврата релевантных соответствий.
* Ограничьте результаты поиска.
* Изучите различия между типами результатов.
* Прочитайте структуру Поиск-ответ адреса.

## <a name="prerequisites"></a>Предварительные условия

Для вызова API-интерфейсов службы Azure Maps требуется учетная запись Azure Maps и ключ. Дополнительные сведения см. в разделе [Создание учетной записи](quick-demo-map-app.md#create-an-account-with-azure-maps) и [Получение первичного ключа](quick-demo-map-app.md#get-the-primary-key-for-your-account). 

Сведения о проверке подлинности в Azure Maps см. [в разделе Управление проверкой подлинности в Azure Maps](./how-to-manage-authentication.md).

> [!TIP]
> Чтобы запросить Служба поиска, можно использовать [приложение Posting](https://www.getpostman.com/apps) для создания вызовов RESTful. Также можно использовать любую предпочтительную среду разработки API.

## <a name="best-practices-to-geocode-addresses"></a>Рекомендации по геокодированию адресов

При поиске полного или частичного адреса с помощью Azure Maps Служба поиска API считывает ключевые слова из поискового запроса. Затем он возвращает координаты долготы и широты адреса. Этот процесс называется *геокодированием*. 

Возможность геокодирования в стране зависит от доступности дорожных данных и точности геокодирование службы. Дополнительные сведения о Azure Maps возможности геокодирования по странам или регионам см. в разделе [покрытие геокодирования](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage).

### <a name="limit-search-results"></a>Ограничение результатов поиска

 API Azure Maps поиска поможет ограничить результаты поиска соответствующим образом. Вы ограничиваете результаты, чтобы можно было отображать нужные данные для пользователей.

> [!NOTE]
> API поиска поддерживают больше параметров, чем только те, которые обсуждаются в этой статье.

#### <a name="geobiased-search-results"></a>Результаты поиска по геосмещению

Чтобы выполнить геосдвиг результатов в соответствующую область для пользователя, всегда добавляйте как можно больше сведений о расположении. Может потребоваться ограничить результаты поиска, указав некоторые типы входных данных:

* Задайте `countrySet` параметр. Его можно задать `US,FR`, например. По умолчанию API выполняет поиск по всему миру, поэтому он может возвращать ненужные результаты. Если в запросе нет `countrySet` параметра, поиск может вернуть неточные результаты. Например, Поиск города с именем *Бельвью* возвращает результаты из США и Франции, так как обе страны содержат город с именем *Бельвью*.

* Для задания ограничивающего `btmRight` прямоугольника можно использовать параметры и `topleft` . Эти параметры ограничивают Поиск определенной областью на карте.

* Чтобы повлиять на область релевантности для результатов, определите параметры `lat` координат `lon` и. Используйте `radius` параметр, чтобы задать радиус области поиска.


#### <a name="fuzzy-search-parameters"></a>Параметры нечеткого поиска

Рекомендуется использовать [нечеткий API Azure Maps поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) , если вы не знакомы с входными данными пользователя для поискового запроса. API сочетает ДОСТОПРИМЕЧАТЕЛЬНОСТИный Поиск и геокодирование с каноническим *однострочным поиском*: 

* Параметры `minFuzzyLevel` и `maxFuzzyLevel` помогают возвращать соответствующие соответствия даже в том случае, если параметры запроса не точно соответствуют необходимой пользователю информации. Чтобы повысить производительность и сократить необычные результаты, задайте для поисковых запросов значения `minFuzzyLevel=1` по `maxFuzzyLevel=2`умолчанию и. 

    Например, если для `maxFuzzyLevel` параметра задано значение 2, условие поиска *рестрант* сопоставляется с *рестораном*. При необходимости можно переопределить нечеткие уровни по умолчанию. 

* Используйте `idxSet` параметр, чтобы задать приоритеты для точного набора типов результатов. Чтобы задать приоритеты для точного набора результатов, можно отправить разделенный запятыми список индексов. В списке порядок элементов не имеет значения. Azure Maps поддерживает следующие индексы:

* `Addr` - **Диапазоны адресов**: адресные точки, которые интерполируются с начала и с конца улицы. Эти точки представлены в виде диапазонов адресов.
* `Geo` - **Географические**регионы: административные подразделения земли. Географией может быть страна, штат или город, например.
* `PAD` - **Адреса точек**: адреса, включающие название и номер улицы. Адреса точек можно найти в индексе. Например, *Сокуел Dr 2501*. Адрес точки обеспечивает максимальный уровень точности, доступный для адресов.  
* `POI` - **Интересующие вас точки**: точки на карте, которые считаются важными для внимания или которые могут представлять интерес. [API адреса поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) не возвращает интереса.  
* `Str` - **Улиц**: улиц на карте.
* `XStr` - **Перекрестное или**пересечение: соединения или места, в которых пересекаются два улицы.


#### <a name="usage-examples"></a>Примеры использования

* `idxSet=POI`— Поиск только в интереса. 

* `idxSet=PAD,Addr`— Поиск только по адресам. `PAD`Указывает адрес точки и `Addr` указывает диапазон адресов.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Обратная географическая код и фильтр для типа сущности geography

При выполнении поиска по обратному коду в [API обратного поиска адреса](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)служба может вернуть многоугольники для административных областей.Чтобы уменьшить область поиска до конкретных типов географических объектов, включите `entityType` параметр в запросы. 

Полученный ответ содержит идентификатор географии и тип сущности, которая была сопоставлена. Если вы предоставляете более одной сущности, конечная точка возвращает *наименьшую доступную сущность*. Полученный идентификатор геометрии можно использовать для получения геометрии географического объекта через [службу поиска многоугольников](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

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

### <a name="set-the-results-language"></a>Задание языка результатов

Используйте `language` параметр, чтобы задать язык для возвращаемых результатов поиска. Если в запросе не задан язык, по умолчанию Служба поиска использует наиболее распространенный язык в стране или регионе. Если на указанном языке нет доступных данных, используется язык по умолчанию. 

Дополнительные сведения см. в статье [Azure Maps Поддерживаемые языки](https://docs.microsoft.com/azure/azure-maps/supported-languages).


### <a name="use-predictive-mode-automatic-suggestions"></a>Использовать прогнозирующий режим (автоматические предложения)

Чтобы найти другие совпадения для частичных запросов, задайте `typeahead` для `true`параметра значение. Этот запрос интерпретируется как часть входных данных, а поиск переходит в прогнозирующий режим. Если не задать для `typeahead` `true`параметра значение, то служба предполагает, что все соответствующие сведения переданы.

В следующем примере запроса служба поиска адреса запрашивается для *Майкрософт*. Здесь для `typeahead` `true`параметра задано значение. Ответ показывает, что служба поиска интерпретирует запрос как частичный запрос. Ответ содержит результаты для автоматически предлагаемого запроса.

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


### <a name="encode-a-uri-to-handle-special-characters"></a>Кодирование URI для управления специальными символами 

Чтобы найти перекрестные адреса, необходимо закодировать универсальный код ресурса (URI) для работы с специальными символами в адресе. Рассмотрим этот пример адреса: *1-ый дом & Union, Сиэтл*. Здесь перед отправкой запроса необходимо`&`закодировать символ амперсанда (). 

Рекомендуется кодировать символьные данные в URI. В URI все символы кодируются с помощью знака процента (`%`) и шестнадцатеричного значения, состоящее из двух символов, которое соответствует коду UTF-8.

#### <a name="usage-examples"></a>Примеры использования

Начните с этого адреса:

```
query=1st Avenue & E 111th St, New York
```

Закодировать адрес:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

Можно использовать следующие методы.

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

Ruby
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Попасть
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>Рекомендации по поиску достопримечательности

В достопримечательности поиска можно запросить результаты достопримечательности по имени. Например, можно искать бизнес по имени. 

Мы настоятельно рекомендуем использовать `countrySet` параметр, чтобы указать страны, в которых приложение нуждается в покрытии. Поведение по умолчанию — поиск по всему миру. Этот широкий поиск может вернуть ненужные результаты, и поиск может занять много времени.

### <a name="brand-search"></a>Поиск фирменной символики

Чтобы улучшить релевантность результатов и информации в ответе, ответ на запрос поиска достопримечательности включает сведения о торговой марке. Эти сведения можно использовать для дальнейшей обработки ответа.

В запросе можно отправить разделенный запятыми список фирменных имен. Используйте список, чтобы ограничить результаты определенными торговыми марками, `brandSet` задав параметр. В списке порядок элементов не имеет значения. При предоставлении нескольких списков торговых марок возвращаемые результаты должны принадлежать по крайней мере к одному из списков.

Чтобы изучить Поиск торговой марки, создадим запрос на [Поиск по категории Достопримечательности](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) . В следующем примере мы будем искать станции газа рядом с кампусом Майкрософт в Редмонде, штат Вашингтон. В ответе отображаются сведения о торговой марке для каждого возвращенного достопримечательности.

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

С помощью API поиска достопримечательности можно искать аэропорты, используя официальный код. Например, вы можете использовать *Sea* для поиска аэропорта-Tacoma в международном офисе: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Поиск поблизости

Чтобы получить результаты достопримечательности в определенном расположении, можно попробовать использовать [API поиска поблизости](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby). Конечная точка возвращает только результаты достопримечательности. Он не принимает параметр поискового запроса. 

Чтобы ограничить результаты, рекомендуется задать радиус.

## <a name="understanding-the-responses"></a>Основные сведения о ответах

Давайте найдем адрес в Сиэтле, выполнив запрос на поиск адреса Azure Maps Служба поиска. В следующем URL-адресе запроса мы устанавливаем `countrySet` параметр для `US` поиска адреса в США.

### <a name="sample-query"></a>Пример запроса

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Поддерживаемые типы результатов

* **Адрес точки**: указывает на карту с указанным адресом, названием и номером улицы. Адрес точки обеспечивает максимальный уровень точности адресов. 

* **Диапазон адресов**: диапазон адресных точек, которые интерполируются с начала и с конца улицы.  

* **География**: области на карте, представляющие административные разделы земли, например страна, штат или город. 

* **Достопримечательности**: указывает на карту, на которой стоит обратить внимание и которые могут представлять интерес.

* **Улица**: улиц на карте. Адреса разрешаются в координаты широты и долготы улицы, содержащей адрес. Номер дома может быть не обработан. 

* **Перекрестная улица**: пересечения. Перекрестный домен представляет соединения, в которых пересекаются два улиц.

### <a name="response"></a>Ответ

Рассмотрим структуру ответа. В следующем ответе типы результирующих объектов отличаются. Если смотреть внимательно, вы увидите три типа результирующих объектов:

* Адрес точки
* Улица
* Перекрестная улица

Обратите внимание, что поиск адреса не возвращает интереса.  

`Score` Параметр для каждого объекта ответа показывает, как показатель сопоставления относится к оценкам других объектов в том же ответе. Дополнительные сведения о параметрах объекта ответа см. в разделе [Получение адреса поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress).

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

Тип отклика *Geometry* может включать идентификатор геометрии, возвращаемый в `dataSources` объекте в `geometry` и. `id` Например, [службу поиска многоугольников](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) можно использовать для запроса геометрических данных в формате геоjson. Используя этот формат, можно получить структуру города или аэропорта для набора сущностей. Затем эти данные границ можно использовать для [настройки геозоны](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) или [поиска интереса внутри геометрии](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


Ответы для API [адреса поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) или API [нечеткого поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) могут включать идентификатор геометрии, возвращаемый в `dataSources` объекте в `geometry` и: `id`


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения см. в следующих статьях:

> [!div class="nextstepaction"]
> [Создание запросов Azure Maps Служба поиска](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

> [!div class="nextstepaction"]
> [Документация по API Служба поиска](https://docs.microsoft.com/rest/api/maps/search)