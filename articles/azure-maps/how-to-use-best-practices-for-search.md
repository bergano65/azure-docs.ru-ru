---
title: Эффективное Поиск с помощью Azure Maps Служба поиска | Карты Microsoft Azure
description: Узнайте, как применять рекомендации для службы поиска с помощью карт Microsoft Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 68c7408f13027ded7beaabf46fb663217a90c52b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845751"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Рекомендации по использованию Azure Maps Служба поиска

Azure Maps [Служба поиска](https://docs.microsoft.com/rest/api/maps/search) включает интерфейсы API с различными возможностями. Например, API поиска адресов используется для поиска интересующей точки (достопримечательности) или данных, связанных с конкретным расположением. В этой статье описываются методики, применяемые при вызове данных из служб поиска Azure Maps. Вы научитесь:

* Построение запросов для возврата релевантных соответствий
* Ограничение результатов поиска
* Изучите разницу между различными типами результатов
* Чтение структуры ответа на поиск адреса


## <a name="prerequisites"></a>Технические условия

Чтобы выполнить вызовы API службы Maps, требуется учетная запись Azure Maps и ключ. При необходимости следуйте инструкциям в разделе [Создание учетной записи](quick-demo-map-app.md#create-an-account-with-azure-maps) и [Получение первичного ключа](quick-demo-map-app.md#get-the-primary-key-for-your-account). Дополнительные сведения о проверке подлинности в Azure Maps см. в разделе [Управление проверкой подлинности в Azure Maps](./how-to-manage-authentication.md).

> [!Tip]
> Чтобы выполнить запрос к службе поиска, можно использовать [приложение posts](https://www.getpostman.com/apps) для создания вызовов RESTful или использовать любую предпочтительную среду разработки API.


## <a name="best-practices-for-geocoding-address-search"></a>Рекомендации по геокодированию (Поиск адресов)

При поиске полного или частичного адреса с помощью Azure Maps Служба поиска API считывает ключевые слова из поискового запроса и возвращает координаты долготы и широты адреса. Этот процесс называется геокодированием. Возможность геокодирования в стране зависит от данных о дорогах и точности геокодирования соответствующей службы.

Дополнительные сведения о Azure Maps возможности геокодирования по странам/регионам см. в разделе [покрытие геокодирования](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) .

### <a name="limit-search-results"></a>Ограничение результатов поиска

 API Azure Maps поиска позволяет ограничить результаты поиска соответствующим образом, чтобы можно было отображать нужные данные для пользователей.

   > [!Note]
   > Ниже перечислены некоторые поддерживаемые параметры API поиска.

   **Результаты поиска геосмещений**

   Чтобы геосдвигировать результаты в соответствующую область для пользователя, всегда следует добавлять максимально возможное подробное расположение. Чтобы ограничить результаты поиска, рассмотрите возможность добавления следующих входных типов:

   1. Задайте параметр `countrySet`, например "US, FR". Поведение поиска по умолчанию заключается в поиске по всему миру, потенциально возвращая ненужные результаты. Если в запросе есть параметр `countrySet`, поиск может вернуть неточные результаты. Например, Поиск города с именем **Бельвью** возвратит результаты из США и Франции, так как в Брос Франции и США есть города с именем **Бельвью** .

   2. Можно использовать параметры `btmRight` и `topleft`, чтобы задать ограничивающий прямоугольник, чтобы ограничить поиск определенной областью на карте.

   3. Чтобы повлиять на область релевантности результатов, можно определить `lat`и `lon` параметры координат и задать радиус области поиска с помощью параметра `radius`.


   **Параметры нечеткого поиска**
   
  Azure Maps [API нечеткого поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) рекомендуется использовать, если неизвестно, какие данные пользователь вводит для поискового запроса. API сочетает в себе Поиск точки интересов (достопримечательности) и геокодирование в каноническом *однострочном поиске*.

   1. `minFuzzyLevel` и `maxFuzzyLevel` помогают возвращать соответствующие соответствия, даже если параметры запроса в точности не совпадают с нужными сведениями. Чтобы повысить производительность и сократить необычные результаты, поисковые запросы по умолчанию `minFuzzyLevel=1` и `maxFuzzyLevel=2`. Возьмем пример условия поиска "рестрант", оно соответствует "ресторану", когда `maxFuzzyLevel` имеет значение 2. По умолчанию нечеткие уровни можно переопределить по мере необходимости.  

   2. Можно также задать приоритеты для точного набора возвращаемых типов результатов с помощью параметра `idxSet`. Для этой цели можно отправить разделенный запятыми список индексов. порядок элементов не имеет значения. Поддерживаются следующие индексы:

       * `Addr`**диапазоны адресов** - : для некоторых улиц есть точки с адресами, которые интерполируются с начала и с конца улицы. Эти точки представлены в виде диапазонов адресов.
       * `Geo` - **географических регионах**: области на карте, представляющие административное деление земли, то есть страны, штата, города.
       * `PAD`**адрес точки** - : указывает на карту, где в индексе можно найти конкретный адрес с названием улицы и номером, например сокуел Dr 2501. Это значение Идкссет является самым высоким уровнем точности, доступного для адресов.  
       * `POI` - **интересующие вас моменты**: точки на карте, которые стоит обратить внимание и могут быть интересными.  [Получение адреса поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) не возвращает интереса.  
       * `Str` - **улиц**: представление улиц на карте.
       * `XStr` - **Cross-улиц/пересечения**: представление соединений; места, где два пересечения улиц.


       **Примеры использования**:

       * Идкссет = достопримечательности (только точки поиска по интересу) 

       * Идкссет = PAD, addr (только поиск адресов, PAD = адрес точки, addr = диапазон адресов)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Фильтровать тип сущности "геокод" и "географическая"

При выполнении обратного поиска по геокодированию с помощью [обратного API адреса поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)служба имеет возможность возвращать многоугольники для административных областей. Предоставляя параметр `entityType` в запросе, можно уменьшить поиск указанных типов сущностей geography. Полученный ответ будет содержать идентификатор географии и соответствующий тип сущности. Если вы предоставляете несколько сущностей, конечная точка возвратит **наименьшую доступную сущность**. Возвращенный идентификатор геометрии можно использовать для получения геометрии географического объекта через [службу получения многоугольников](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Пример запроса:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Ответ.**

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

Параметр `language` позволяет выбрать язык результатов, возвращаемых API. Если язык не задан в запросе, служба поиска автоматически по умолчанию будет иметь наиболее распространенный язык в стране или регионе. Кроме того, если данные на указанном языке недоступны, используется язык по умолчанию. Список поддерживаемых языков см. в статье [Поддерживаемые](https://docs.microsoft.com/azure/azure-maps/supported-languages) языки Azure Maps служб по странам или регионам.


### <a name="predictive-mode-autosuggest"></a>Прогнозирующий режим (автозаполнение)

Чтобы найти другие совпадения для частичных запросов, параметру `typeahead` необходимо присвоить значение true. Запрос будет интерпретирован как частичный вход, и поиск будет выполнен в прогнозном режиме. В противном случае в службе предполагается, что все соответствующие сведения переданы.

В приведенном ниже образце запроса можно увидеть, что служба адреса поиска запрашивает "Майкрософт" с параметром `typeahead`, для которого установлено значение **true**. Если вы видите ответ, можно увидеть, что служба поиска интерпретирует запрос как частичный запрос. Ответ содержит результаты для автопредложенного запроса.

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


### <a name="uri-encoding-to-handle-special-characters"></a>Кодирование URI для управления специальными символами 

Чтобы найти перекрестные адреса, необходимо закодировать универсальный код ресурса (URI) для работы с специальными символами в адресе. Рассмотрим этот пример адреса: "1-й дом & профсоюз, Сиэтл". Перед отправкой запроса Специальный символ "&" должен быть закодирован. Мы рекомендуем кодировать символьные данные в URI, где все символы кодируются с помощью символа "%" и шестнадцатеричного значения из двух символов, соответствующего символу UTF-8.

**Примеры использования**:

Получить адрес для поиска:

```
query=1st Avenue & E 111th St, New York
```

 должен быть закодирован как:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
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

PHP:
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


## <a name="best-practices-for-poi-search"></a>Рекомендации по достопримечательности поиску

Поиск по интересам (достопримечательности) позволяет запрашивать результаты достопримечательности по имени, например, искать бизнес по имени. Мы настоятельно рекомендуем использовать параметр `countrySet`, чтобы указать страны, в которых приложение нуждается в покрытии, так как поведение по умолчанию будет искать весь мир, потенциально возвращая ненужные результаты и/или выполняя более длительное время поиска.

### <a name="brand-search"></a>Поиск фирменной символики

Для повышения релевантности результатов и информации в ответе на запрос поиска в ответе (достопримечательности) включаются сведения о торговой марке, которые можно использовать для анализа ответа.

В запросе также можно отправить разделенный запятыми список фирменных имен. Список можно использовать для ограничения результатов определенными торговыми марками с помощью параметра `brandSet`. Порядок элементов не имеет значения. Если предоставлено несколько торговых марок, возвращаются только результаты, принадлежащие (по крайней мере) к одному из указанных списков.

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

Поиск достопримечательности поддерживает поиск в аэропортах с помощью официальных кодов аэропорта. Например, **Sea** (Сиэтл-Tacoma Международный аэропорт). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Поиск поблизости

Чтобы получить только результаты достопримечательности в определенном расположении, можно выбрать [ближайший API поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) . Эта конечная точка будет возвращать только результаты достопримечательности и не принимает параметр поискового запроса. Чтобы ограничить результаты, рекомендуется задать радиус.

## <a name="understanding-the-responses"></a>Основные сведения о ответах

Давайте создадим запрос на поиск адреса в [службе поиска](https://docs.microsoft.com/rest/api/maps/search) Azure Maps для адреса в Сиэтле. Если вы внимательно взгляните на URL-адрес запроса, приведенный ниже, мы задали параметр `countrySet` **, чтобы найти** адрес в США Америке.

**Пример запроса**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Рассмотрим приведенную ниже структуру ответа. Результирующие типы результирующих объектов в ответе различаются. При внимательном рассмотрении можно увидеть, что у нас есть три различных типа результирующих объектов: "адрес точки", "улица" и "перекрестный дом". Обратите внимание, что поиск адреса не возвращает интереса. Параметр `Score` для каждого объекта ответа указывает относительный показатель сопоставления с результатами других объектов в том же ответе. Дополнительные сведения о параметрах объекта Response см. в разделе [Получение адреса поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) .

**Поддерживаемые типы результатов:**

* **Адрес точки:** Указывает на карту с указанным адресом, названием и номером улицы. Максимальный уровень точности, доступный для адресов. 

* **Диапазон адресов:**  Для некоторых улиц есть точки с адресами, которые интерполируются с начала и с конца улицы; Эти точки представлены в виде диапазонов адресов. 

* **География:** Области на карте, представляющие административное деление земли, то есть страна, штат, город. 

* **Достопримечательности — (интересующие точки):** Баллы на карте, которые стоит обратить внимание и могут представлять интерес.

* **Улица:** Представление улиц на карте. Адреса разрешаются в координаты широты и долготы улицы, содержащей адрес. Номера домов могут не учитываться. 

* **Перекрестная Улица:** Пересечения. Представления соединений; места, где два пересечения улиц.

**Ответ.**

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

Если тип отклика — **Geometry**, он может содержать идентификатор геометрии, возвращаемый в объекте **DataSources** в области "Geometry" и "ID". Например, [Служба "получение многоугольников](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) " позволяет запрашивать геометрические данные в формате геоjson. Например, «город» или «структура аэропорта» для набора сущностей. Эти граничные данные можно использовать для [геозоны](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) или [поиска интереса внутри геометрии](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


[Адрес поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) или ответы API [нечеткого поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) могут содержать **идентификатор геометрии** , возвращаемый в объекте DataSources в области "Geometry" и "ID".


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте [, как создавать запросы к службе поиска Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Изучите [документацию по API службы поиска](https://docs.microsoft.com/rest/api/maps/search)Azure Maps. 
