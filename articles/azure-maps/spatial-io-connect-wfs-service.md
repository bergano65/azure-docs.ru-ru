---
title: Подключение к службе веб-компонентов (ВФС) | Карты Microsoft Azure
description: Узнайте, как подключиться к службе ВФС, а затем запросите службу ВФС с помощью веб-пакета SDK Azure Maps и модуля пространственного ввода/вывода.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0f50fe51f9c1cdef3c3f07c91640f5b9b9616229
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370964"
---
# <a name="connect-to-a-wfs-service"></a>Подключение к службе ВФС

Служба веб-компонентов (ВФС) — это веб-служба для запроса пространственных данных с стандартизованным API, который определен Открытый геопространственный консорциум (OGC). Класс `WfsClient` в модуле пространственных операций ввода-вывода позволяет разработчикам подключаться к службе ВФС и запрашивать данные из службы.

Класс `WfsClient` поддерживает следующие функции:

- Поддерживаемые версии: `1.0.0`, `1.1.0`и `2.0.0`
- Поддерживаемые операторы фильтров: двоичные сравнения, логические, математические, значения и `bbox`.
- Запросы выполняются только с помощью `HTTP GET`.
- Поддерживаемые операции:

    | | |
    | :-- | :-- |
    | Возможности | Создает документ метаданных с допустимыми операциями и параметрами ВФС |
    | Функцияического | Возвращает набор компонентов из источника данных |
    | дескрибефеатуретипе | Возвращает поддерживаемые типы компонентов |

## <a name="using-the-wfs-client"></a>Использование клиента ВФС

Класс `atlas.io.ogc.WfsClient` в модуле пространственных операций ввода-вывода упрощает выполнение запросов к службе ВФС и преобразование ответов в геообъектные объекты JSON. Затем этот объект геоjson можно использовать для других целей сопоставления.

Следующий код выполняет запрос к службе ВФС и отображает возвращенные функции на карте.

<br/>

<iframe height='700' scrolling='no' title='Пример простого ВФС' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>Пример простой ВФС</a> с пером Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Поддерживаемые фильтры

Спецификация для стандарта ВФС использует фильтры OGC. Перечисленные ниже фильтры поддерживаются клиентом ВФС, предполагая, что вызываемая служба также поддерживает эти фильтры. Строки настраиваемых фильтров можно передать в класс `CustomFilter`.

**Логические операторы**

- `And`
- `Or`
- `Not`

**Операторы значений**

- `GmlObjectId`
- `ResourceId`

**Математические операторы**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Операторы сравнения**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

В следующем коде показано использование различных фильтров с клиентом ВФС.

<br/>

<iframe height='500' scrolling='no' title= 'Примеры фильтров ВФС' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Дополнительные сведения см. в разделе<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>Примеры фильтров ВФС</a> для <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>ВФС Service Explorer

В следующем коде для изучения служб ВФС используется клиент ВФС. Выберите слой типа свойства в службе и просмотрите связанные условные обозначения.

<br/>

<iframe height='700' scrolling='no' title= 'ВФС Service Explorer' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. раздел <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>ВФС Service Explorer</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

Вы также можете использовать прокси-службу для загрузки ресурсов, размещенных в доменах, которые не включены в CORs. Сначала необходимо определить переменную для хранения URL-адреса службы прокси-сервера и задать параметр `proxyService` для клиента ВФС. Чтобы отобразить параметр прокси-службы для пользователя, добавьте пользовательский ввод в пользовательский интерфейс. Загрузка URL-адреса службы при нажатии на вход. В следующих фрагментах кода показано, как использовать прокси-службу.

```JavaScript

//A variable to hold the URL of the proxy service
var proxyServiceUrl = window.location.origin + 'CorsEnabledProxyService.ashx?url=';

//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: (document.getElementById('useProxyService').checked) ? proxyServiceUrl : null
});

function proxyOptionChanged() {
    if (currentServiceUrl) {
        loadClient(currentServiceUrl);
    }
}

```

Приведенный ниже фрагмент кода HTML соответствует приведенному выше коду JavaScript:

```html
<!-- use the proxy service -->
<input id="useProxyService" type="checkbox" onclick="proxyOptionChanged()"/>
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [вфсклиент](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [вфссервицеоптионс](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Использование основных операций](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Сведения о поддерживаемых форматах данных](spatial-io-supported-data-format-details.md)
