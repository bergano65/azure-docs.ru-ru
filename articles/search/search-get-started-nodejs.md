---
title: Краткое руководство. Создание индекса службы "Поиск Azure" в Node.js с помощью интерфейсов REST API
description: Пример для Поиска Azure на Node.js, который демонстрирует создание, загрузку данных и выполнение запросов из JavaScript.
author: lobrien
manager: nitinme
tags: azure-portal
services: search
ms.service: search
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/10/2019
ms.author: laobri
ms.openlocfilehash: 4e17247ea412b5472a0c23fd74ff7e53f375710d
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881499"
---
# <a name="quickstart-create-an-azure-search-index-in-nodejs-using-rest-apis"></a>Краткое руководство. Создание индекса службы "Поиск Azure" в Node.js с помощью интерфейсов REST API
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Портал](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Из этой статьи вы узнаете, как создать приложение Node. js, которое создает, загружает индекс службы "Поиск Azure" и отправляет к нему запросы. Здесь вы найдете пошаговое руководство по созданию приложения. Вы также можете [загрузить исходный код и данные](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/) и запустить приложение из командной строки.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

В этом кратком руководстве используются приведенные ниже службы, инструменты и данные.

+ [Node.js](https://nodejs.org).
+ Node.js должен установить [NPM](https://www.npmjs.com).
+ Примерная структура индекса и соответствующие документы приведены в этой статье и в [каталоге **quickstart** в репозитории](https://github.com/Azure-Samples/azure-search-javascript-samples/).
+ [Создайте службу "Поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. Вы можете использовать бесплатную службу для выполнения инструкций, описанных в этом кратком руководстве.

Рекомендация:

* [Visual Studio Code](https://code.visualstudio.com).
* Расширения [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) и [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) для VSCode.

<a name="get-service-info"></a>
## <a name="get-keys-and-urls"></a>Получение ключей и URL-адресов

Вызовы к службе требуют конечную точку URL-адреса и ключ доступа при каждом запросе. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **Обзор** для службы поиска получите ее имя. Имя службы можно проверить, просмотрев URL-адрес конечной точки. Если URL-адрес конечной точки имеет вид `https://mydemo.search.windows.net`, значит служба называется `mydemo`.

2. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

    Получите также ключ запроса. Мы рекомендуем создавать запросы с доступом только для чтения.

![Получение имени службы, ключей запросов и администратора](media/search-get-started-nodejs/service-name-and-keys.png)

Для выполнения любого запроса к службе нужно включить ключ API в заголовок. Действительный ключ устанавливает для каждого запроса отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

## <a name="set-up-your-environment"></a>Настройка среды

Для начала откройте консоль PowerShell или другую среду, в которой установлен Node.js.

1. Создайте каталог разработки и присвойте ему имя `quickstart`:

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. Инициализируйте пустой проект с помощью NPM, выполнив `npm init`. Примите значения по умолчанию, за исключением лицензии, которая должна иметь значение MIT. 

1. Добавьте пакеты, от которых будет зависеть ваш код и которые помогут в разработке:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Убедитесь, что вы настроили проекты и зависимости. Файл **package.json** должен выглядеть примерно так:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Azure_Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "nconf": "^0.10.0",
        "node-fetch": "^2.6.0"
      },
      "devDependencies": {
        "eslint": "^6.1.0",
        "eslint-config-airbnb-base": "^13.2.0",
        "eslint-config-prettier": "^6.0.0",
        "eslint-plugin-import": "^2.18.2",
        "prettier": "^1.18.2"
      }
    }
    ```
Создайте файл **azure_search_config.json** для данных службы поиска:

```json
{
    "serviceName" : "[SERVICE_NAME]",
    "adminKey" : "[ADMIN_KEY]",
    "queryKey" : "[QUERY_KEY]",
    "indexName" : "hotels-quickstart"
}
```

Замените значение `[SERVICE_NAME]` именем службы поиска. Замените `[ADMIN_KEY]` и `[QUERY_KEY]` значениями ключей, которые вы записали ранее. 

## <a name="1---create-index"></a>1\. Создание индекса 

Создайте файл **hotels_quickstart_index.json**.  Этот файл определяет, как Поиск Azure будет работать с документами, которые вы отправите на следующем шаге. Каждое поле идентифицируется по свойству `name`, и для каждого поля указано значение `type`. У каждого поля также есть ряд атрибутов индекса, которые определяют, может ли Поиск Azure применять это поле для поиска, фильтрации, сортировки и определения аспектов. Большинство полей имеют простой тип данных, но некоторые (например, `AddressType`) являются сложными типами, что позволяет создавать сложные структуры данных в индексе.  Вы можете подробнее изучить [поддерживаемые типы данных](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) и [атрибуты индекса](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes). 

Добавьте следующий код в **hotels_quickstart_index.json** или [скачайте готовый файл](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels_quickstart_index.json). 

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```
    

Мы рекомендуем отделять особенности конкретного сценария от кода, который будет применяться более широко. Класс `AzureSearchClient`, определенный в файле **AzureSearchClient.js**, умеет создавать URL-адреса запросов, выполнять запрос через Fetch API и реагировать на код состояния ответа.

Начните работу с **AzureSearchClient.js**, импортировав пакет **node-fetch** и создав простой класс. Изолируйте изменяемые части класса `AzureSearchClient`, передав в его конструктор разные значения конфигурации:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    constructor(searchServiceName, adminKey, queryKey, indexName) {
        this.searchServiceName = searchServiceName;
        this.adminKey = adminKey;
        // The query key is used for read-only requests and so can be distributed with less risk of abuse.
        this.queryKey = queryKey;
        this.indexName = indexName;
        this.apiVersion = '2019-05-06';
    }

    // All methods go inside class body here!
}

module.exports = AzureSearchClient;
```

Первой задачей этого класса является создание URL-адресов, на которые будут отправляться запросы. Создайте эти URL-адреса с помощью методов экземпляра, которые используют данные конфигурации, переданные в конструктор класса. Обратите внимание, что создаваемый URL-адрес зависит от версии API и должен иметь аргумент, указывающий эту версию (в нашем приложении это `2019-05-06`). 

Первый из этих методов возвратит URL-адрес самого индекса. Добавьте следующий метод в тело класса:

```javascript
getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

Следующая задача `AzureSearchClient` — асинхронный запрос через Fetch API. Асинхронный статический метод `request` принимает URL-адрес, строку с названием метода HTTP ("GET", "PUT", "POST", "DELETE"), ключ для использования в запросе и необязательный объект JSON. Переменная `headers` сопоставляет `queryKey` (ключ администратора или доступный только для чтения ключ запроса) с заголовком HTTP-запроса "api-key". Параметры запроса всегда содержат используемое значение `method` и `headers`. Если значение `bodyJson` отлично от `null`, телу HTTP-запроса присваивается строковое представление `bodyJson`. Метод `request` возвращает обещание Fetch API о выполнении HTTP-запроса.

```javascript
static async request(url, method, apiKey, bodyJson = null) {
    // Uncomment the following for request details:
    /*
    console.log(`\n${method} ${url}`);
    console.log(`\nKey ${apiKey}`);
    if (bodyJson !== null) {
        console.log(`\ncontent: ${JSON.stringify(bodyJson, null, 4)}`);
    }
    */

    const headers = {
        'content-type' : 'application/json',
        'api-key' : apiKey
    };
    const init = bodyJson === null ?
        { 
            method, 
            headers
        }
        : 
        {
            method, 
            headers,
            body : JSON.stringify(bodyJson)
        };
    return fetch(url, init);
}
```

Для тестовых целей достаточно создать исключение, если HTTP-запрос завершится неудачей. В реальных приложениях вы, скорее всего, захотите вести журнал и выполнять диагностику кода состояния HTTP `response`, полученного от запроса к службе поиска. 
    
```javascript
static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
}
```

Наконец, добавьте методы обнаружения, удаления и создания индекса Поиска Azure. Все эти методы имеют одинаковую структуру:

* Получение конечной точки, к которой нужно отправлять запрос.
* Создание запроса с нужными значениями конечной точки, HTTP-команды, ключа API и, если требуется, текста JSON. `indexExistsAsync()` и `deleteIndexAsync()` не используют текст JSON, в отличие от `createIndexAsync(definition)`.
* Оператор `await` ожидает ответ на запрос.  
* Выполнение действия в ответ на код состояния из ответа.
* Возвращение обещанию некоторого допустимого значения (логическое значение, `this` или результаты запроса). 

```javascript
async indexExistsAsync() { 
    console.log("\n Checking if index exists...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "GET", this.adminKey);
    // Success has a few likely status codes: 200 or 204 (No Content), but accept all in 200 range...
    const exists = response.status >= 200 && response.status < 300;
    return exists;
}

async deleteIndexAsync() {
    console.log("\n Deleting existing index...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "DELETE", this.adminKey);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}

async createIndexAsync(definition) {
    console.log("\n Creating index...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "PUT", this.adminKey, definition);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

Убедитесь, что методы находятся внутри класса и вы экспортируете этот класс. Внешняя область **AzureSearchClient.js** должна иметь следующее значение:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

Объектно-ориентированный класс был хорошим выбором для модуля **AzureSearchClient.js**, который потенциально можно использовать многократно, но он не нужен для основной программы, которую вам нужно поместить в файл с именем **index.js**. 

Создайте **index.js** и поместите в него следующее:

* Пакет **nconf**, который обеспечивает гибкость в указании конфигурации с помощью JSON, переменных среды или аргументов командной строки.
* Данные из файла **hotels_quickstart_index.json**.
* Модуль `AzureSearchClient`.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

[Пакет **nconf**](https://github.com/indexzero/nconf) позволяет задавать данные конфигурации в нескольких форматах, таких как переменные среды или командная строка. В этом примере применяются базовые возможности **nconf**, чтобы считать файл **azure_search_config.json** и вернуть его содержимое в виде словаря. С помощью функции **nconf** `get(key)` вы можете быстро проверить, правильно ли изменены значения конфигурации. В завершение своей работы функция возвращает конфигурацию:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME' ) {
        throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

Функция `sleep` создает объект `Promise`, который разрешается через заданный промежуток времени. Использование этой функции позволяет приложению приостанавливать работу в ожидании завершения и доступности асинхронных операций с индексами. Добавление такой задержки обычно используются только в демонстрациях, тестах и примерах приложений.

```javascript
function sleep(ms) {
    return(
        new Promise(function(resolve, reject) {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Наконец, укажите и вызовите основную асинхронную функцию `run`. Эта функция вызывает другие функции по порядку, ожидая при необходимости разрешения `Promise`.

* Получите конфигурацию с помощью ранее созданной `getAzureConfiguration()`.
* Создайте новый экземпляр `AzureSearchClient`, передав в него значения из конфигурации.
* Проверьте наличие индекса и удалите его, если он существует.
* Создайте индекс с помощью `indexDefinition`, загруженного из **hotels_quickstart_index.json**.

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get["serviceName"]);
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
    } catch (x) {
        console.log(x);
    }
}

run();
```

Не забудьте о последнем вызове `run()`! Это точка входа в программу при выполнении `node index.js` на следующем шаге.

Обратите внимание, что `AzureSearchClient.indexExistsAsync()` и `AzureSearchClient.deleteIndexAsync()` не принимают параметры. Эти функции вызывают `AzureSearchClient.request()` без аргумента `bodyJson`. Поскольку `bodyJson === null` имеет значение `true`, структура `init` в `AzureSearchClient.request()` будет просто глаголом HTTP ("GET" для `indexExistsAsync()` или "DELETE" для `deleteIndexAsync()`) с заголовками, которые указывают ключ запроса.  

И наоборот, метод `AzureSearchClient.createIndexAsync(indexDefinition)` _принимает_ параметр. Функция `run` из `index.js` передает содержимое файла **hotels_quickstart_index.json** в метод `AzureSearchClient.createIndexAsync(indexDefinition)`. Метод `createIndexAsync()` передает это определение в `AzureSearchClient.request()`. Так как `bodyJson === null` теперь имеет значение `false`, структура `init` в `AzureSearchClient.request()` не только содержит глагол HTTP ("PUT") и заголовки, но и указывает `body` как данные определения индекса.

### <a name="prepare-and-run-the-sample"></a>Подготовка и запуск примера

Выполните следующие команды в окне терминала.

1. Перейдите в папку, которая содержит файл **package.json** и остальную часть кода.
1. Установите пакеты для этого примера с помощью `npm install`.  Эта команда скачивает пакеты, от которых зависит код.
1. Запустите программу с помощью `node index.js`.

Вы увидите ряд сообщений с описанием действий, которые выполняются программой. Если вы хотите просмотреть более подробные сведения о запросах, можно раскомментировать [строки в начале метода `AzureSearchClient.request()`]https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/AzureSearchClient.js#L21-L27) в файле **AzureSearchClient.js**. 

Откройте **обзорные сведения** о службе Поиска Azure на портале Azure. Выберите вкладку **Индексы**. Вы должны увидеть нечто вроде этого:

![Снимок экрана. Портала Azure, где открыта обзорная информация о Службе поиска на вкладке "Индексы"](media/search-get-started-nodejs/create-index-no-data.png)

На следующем шаге вы добавите данные в индекс. 

## <a name="2---load-documents"></a>2\. Загрузка документов 

Документы в Поиске Azure представляют собой структуры данных, которые служат входами и (или) выходами для запросов. Необходимо передать эти данные в индекс с помощью запроса POST. Конечная точка для этой операции отличается от той, которая использовалась на предыдущем шаге. Откройте файл **AzureSearchClient.js** и добавьте следующий метод после `getIndexUrl()`:

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

Как и в случае с `AzureSearchClient.createIndexAsync(definition)`, вам потребуется функция для вызова `AzureSearchClient.request()` и передачи данных об отеле в тексте этого запроса. Добавьте в файл **AzureSearchClient.js** элемент `postDataAsync(hotelsData)` после `createIndexAsync(definition)`:

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 Входные документы могут содержать строки базы данных, большие двоичные объекты из хранилища BLOB-объектов (как в этом примере) или сохраненные на диске документы JSON. Вы можете скачать файл [hotels.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels.json) или создать собственный **hotels.json** со следующим содержимым:

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}

```

Чтобы загрузить эти данные в программу, добавьте в **index. js** строку со ссылкой `hotelData` где-то в верхней части:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Теперь измените функцию `run()` в **index. js**. Индекс станет доступным с небольшой задержкой, поэтому добавьте 2-секундную паузу перед вызовом `AzureSearchClient.postDataAsync(hotelData)`:

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
    } catch (x) {
        console.log(x);
    }
}
```

Снова запустите программу командой `node index.js`. Теперь набор сообщений будет немного отличаться от тех, которые вы видели на шаге 1. На этот раз индекс уже _существует_, и вы получите сообщение о его удалении перед тем, как приложение создаст новый индекс и поместит в него данные. 

## <a name="3---search-an-index"></a>3\. Поиск в индексе

Вернитесь к вкладке **Индексы** на странице **обзорных сведений** о службе Поиска Azure на портале Azure. Теперь индекс этот содержит четыре документа и потребляет некоторый объем хранилища (правильное состояние индекса может отображаться в пользовательском интерфейсе с задержкой в несколько минут). Щелкните имя индекса, которое будет передаваться в **обозреватель поиска**. Эта страница позволяет экспериментировать с запросами к данным. Попробуйте выполнить поиск по строке запроса `*&$count=true`, которая должна вернуть все документы и сообщить количество результатов. Попробуйте использовать строку запроса `historic&highlight=Description&$filter=Rating gt 4`, которая должна вернуть один документ со словом historic в тегах `<em></em>`. Узнайте, [как создать запрос в Поиске Azure](https://docs.microsoft.com/azure/search/search-query-overview). 

Воспроизведите эти запросы в коде, открыв файл **index.js** и добавив следующий код в его начало:

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

В том же файле **index.js** создайте функцию `doQueriesAsync()`, показанную ниже. Эта функция принимает объект `AzureSearchClient` и применяет метод `AzureSearchClient.queryAsync` к каждому из значений в массиве `queries`. С помощью функции `Promise.all()` она возвращает один объект `Promise`, который разрешается только при разрешении всех запросов. Вызов `JSON.stringify(body, null, 4)` форматирует результат запроса в более удобный для чтения вид.

```javascript
async function doQueriesAsync(client) {
    return Promise.all(
        queries.map( async query => {
            const result = await client.queryAsync(query);
            const body = await result.json();
            const str = JSON.stringify( body, null, 4);
            console.log(`Query: ${query} \n ${str}`);
        })
    );
}
```

Измените функцию `run()`, добавив достаточно долгую паузу для завершения работы индексатора и вызов функции `doQueriesAsync(client)`:

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
        // Data availability can take a few seconds
        await sleep(5000);
        await doQueriesAsync(client);
    } catch (x) {
        console.log(x);
    }
}
```

Чтобы реализовать `AzureSearchClient.queryAsync(query)`, измените файл **AzureSearchClient.js**. Для поиска требуется другая конечная точка, а условия поиска становятся аргументами URL-адреса, поэтому добавьте функцию `getSearchUrl(searchTerm)` рядом с уже готовыми методами `getIndexUrl()` и `getPostDataUrl()`.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

Функция `queryAsync(searchTerm)` также помещается в файл **AzureSearchClient.js** и имеет такую же структуру, что и `postDataAsync(data)` и другие функции запросов: 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

Поиск выполняется по запросу GET без текста, так как термин для поиска входит в URL-адрес. Обратите внимание, что `queryAsync(searchTerm)` использует `this.queryKey` в отличие от других функций, которые использовали ключ администратора. Ключи запроса, как можно понять по их названию, используются только для запроса по индексу и не позволяют как-либо его изменять. Таким образом, ключи запроса можно более спокойно размещать в клиентских приложениях.

Запустите программу командой `node index.js`. Теперь, помимо описанных выше шагов, будут отправлены запросы и выведены в консоль их результаты.

### <a name="about-the-sample"></a>Сведения о примере

В этом примере используется небольшой объем данных о гостиницах, который позволяет продемонстрировать основные возможности по созданию индекса Поиска Azure и отправке запросов к нему.

Класс **AzureSearchClient** инкапсулирует конфигурацию, URL-адреса и базовые HTTP-запросы для службы поиска. Файл **index.js** загружает данные конфигурации для Поиска Azure и данные о гостиницах, которые будут использоваться для индексирования, в функциях `run`, для заказов и других операций.

Общее поведение функции `run` заключается в том, чтобы удалить индекс службы "Поиск Azure", если он существует, затем создать индекс, добавить данные и выполнить несколько запросов.  

## <a name="clean-up"></a>Очистка 

Если вы работаете в своей подписке, по окончании проекта рекомендуем решить, нужны ли вам созданные ресурсы. Работающие ресурсы могут означать лишние затраты. Можно удалить отдельные ресурсы или удалить группу ресурсов, что позволит удалить весь набор ресурсов.

Просматривать ресурсы и управлять ими можно на портале с помощью ссылок **Все ресурсы** или **Группы ресурсов** на панели навигации слева.
При работе с бесплатной версией службы помните о том, что вам доступно максимум три индекса, индексатора и источника данных. Вы можете удалить отдельные элементы на портале, чтобы не превысить лимит. 

## <a name="next-steps"></a>Дополнительная информация

С помощью этого краткого руководства для Node.js вы выполнили ряд задач по созданию индекса, загрузке документов в него и выполнению запросов. Мы простейшим образом выполнили определенные действия, например чтение конфигурации и определение запросов. В реальном приложении вам будет лучше разместить эти задачи в разных модулях, чтобы обеспечить гибкость и инкапсуляцию. 
 
Если у вас уже есть опыт работы с Поиском Azure, вы можете использовать этот пример как фундамент для средств подбора (запросы опережающего ввода или автозаполнения), фильтров и фасетной навигации. Если вы еще плохо знакомы с Поиском Azure, мы рекомендуем ознакомиться с другими руководствами, чтобы получить представление о его возможностях. Посетите нашу [страницу документации](https://azure.microsoft.com/documentation/services/search/), чтобы найти дополнительные ресурсы. 

> [!div class="nextstepaction"]
> [Вызов Поиска Azure с веб-страницы с помощью JavaScript](https://github.com/liamca/azure-search-javascript-samples)
