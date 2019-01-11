---
title: Создание клиента для использования развернутой веб-службы
titleSuffix: Azure Machine Learning service
description: Узнайте, как использовать веб-службу, которая была создана при развертывании модели Машинного обучения Azure. Эта веб-служба предоставляет REST API. Вы можете создать для этого API клиенты на любом языке программирования по своему усмотрению.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: efa24fcb624c7613ce16028d7ba06af4d4d2153c
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753393"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Использование модели Машинного обучения Azure, развернутой в качестве веб-службы

При развертывании модели Машинного обучения Azure в качестве веб-службы создается интерфейс REST API. Через этот API вы можете отправлять данные в модель и получать от нее прогнозы. В этом документе описано, как создать клиенты для работы с веб-службой на языках C#, Go, Java и Python.

Веб-служба создается при развертывании образа в Экземплярах контейнеров Azure, Службе Azure Kubernetes или Project Brainwave (программируемые пользователем вентильные матрицы). Образы создаются на основе зарегистрированных моделей и файлов оценки. URI для доступа к веб-службе можно получить с помощью [пакета SDK для Машинного обучения Azure](https://aka.ms/aml-sdk). Если включена проверка подлинности, с помощью пакета SDK вы можете получить и ключи проверки подлинности.

Ниже приведен общий рабочий процесс создания клиента, который использует веб-службу Машинного обучения:

1. Получение сведений о подключении с помощью пакета SDK.
1. Определение типа данных запроса, которые используются моделью.
1. Создание приложения, которое обращается к веб-службе.

## <a name="connection-information"></a>Сведения о подключении

> [!NOTE]
> Для получения информации о веб-службе используется пакет SDK для Машинного обучения Azure. Этот пакет SDK написан на Python. Вы можете использовать любой язык для создания клиента для службы.

Класс [azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) предоставляет необходимые сведения для создания клиента. Следующие свойства `Webservice` могут быть полезны при создании клиентского приложения:

* `auth_enabled` — если включена проверка подлинности, имеет значение `True`; в противном случае `False`;
* `scoring_uri` — адрес REST API.

Эти сведения для развернутой веб-службы можно получить тремя способами.

* При развертывании модели, возвращается объект `Webservice` с информацией о службе:

    ```python
    service = Webservice.deploy_from_model(name='myservice',
                                           deployment_config=myconfig,
                                           models=[model],
                                           image_config=image_config,
                                           workspace=ws)
    print(service.scoring_uri)
    ```

* С помощью `Webservice.list` можно получить список развернутых веб-служб для моделей в рабочей области. Добавляя фильтры, можно сузить список возвращаемых сведений. Дополнительные сведения о возможностях фильтрации см. в справочной документации по [Webservice.list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#list).

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    ```

* Если вы знаете имя развернутой службы, создайте новый экземпляр класса `Webservice` и предоставьте ему в качестве параметров имя рабочей области и имя службы. Созданный объект будет содержать сведения о развернутой службе.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    ```

### <a name="authentication-key"></a>ключ проверки подлинности;

При включении проверки подлинности для развертывания автоматически создаются ключи проверки подлинности.

* Проверка подлинности включена по умолчанию при развертывании в Службе Azure Kubernetes.
* Проверка подлинности отключена по умолчанию при развертывании в Экземплярах контейнеров Azure.

Чтобы управлять проверкой подлинности, используйте параметр `auth_enabled` при создании или обновлении развертывания.

Если включена проверка подлинности, можно использовать метод `get_keys` для извлечения первичного и вторичного ключей проверки подлинности:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Если вам нужно повторно создать ключ, используйте [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#regen-key).

## <a name="request-data"></a>Данные запроса

REST API ожидает, что текст запроса содержит документ JSON со следующей структурой:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Структура данных должна совпадать с той, которую ожидают скрипт оценки и модель, размещенные в службе. Скрипт оценки может изменять данные перед передачей в модель.

Например, модель в примере [train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) ожидает получить массив из 10 чисел. Сценарий оценки для этого примера создает из данных запроса массив Numpy и передает его в модель. В следующем примере представлен формат данных, которые ожидает получить эта служба:

```json
{
    "data": 
        [
            [
                0.0199132141783263, 
                0.0506801187398187, 
                0.104808689473925, 
                0.0700725447072635, 
                -0.0359677812752396, 
                -0.0266789028311707, 
                -0.0249926566315915, 
                -0.00259226199818282, 
                0.00371173823343597, 
                0.0403433716478807
            ]
        ]
}
``` 

Веб-служба может принимать в одном запросе несколько наборов данных. Она возвращает документ JSON, содержащий массив ответов.

### <a name="binary-data"></a>Двоичные данные

Если ваша модель принимает двоичные данные, например изображения, необходимо изменить файл `score.py`, используемый для развертывания, чтобы обеспечить прием необработанных HTTP-запросов. Ниже приведен пример файла `score.py`, который принимает двоичные данные и возвращает обращенные байты для запросов POST. Для запросов GET он возвращает полный URL-адрес в тексте ответа:

```python 
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Пространство имен `azureml.contrib` часто изменяется, так как мы работаем над улучшением службы. Поэтому все, что доступно в этом пространстве имен, считается предварительными версиями компонентов, поддержка которых корпорацией Майкрософт ограничена.
>
> Если необходимо протестировать их в локальной среде разработки, можно установить эти компоненты в пространстве имен `contrib`, используя следующую команду:
> 
> ```shell
> pip install azureml-contrib-services
> ```

## <a name="call-the-service-c"></a>Вызов службы с помощью C#

В этом примере демонстрируется вызов веб-службы, созданный на языке C# на основе примера [train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb):

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key
            string scoringUri = "<your web service URI>";
            string authKey = "<your key>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

Возвращаемые результаты аналогичны приведенному ниже документу JSON.

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Вызов службы с помощью Go

В этом примере демонстрируется вызов веб-службы, созданный на языке Go на основе примера [train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb):

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key (if any) for your service
var authKey string = "<your key>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

Возвращаемые результаты аналогичны приведенному ниже документу JSON.

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Вызов службы с помощью Java

В этом примере демонстрируется вызов веб-службы, созданный на языке Java на основе примера [train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb):

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key
        String key = "<your key>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

Возвращаемые результаты аналогичны приведенному ниже документу JSON.

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Вызов службы с помощью Python

В этом примере демонстрируется вызов веб-службы, созданный на языке Python на основе примера [train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb):

```python
import requests
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key
key = '<your key>'

# Two sets of data to score, so we get two results back
data = {"data": 
            [
                [
                    0.0199132141783263, 
                    0.0506801187398187, 
                    0.104808689473925, 
                    0.0700725447072635, 
                    -0.0359677812752396, 
                    -0.0266789028311707, 
                    -0.0249926566315915, 
                    -0.00259226199818282, 
                    0.00371173823343597, 
                    0.0403433716478807
                ],
                [
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303]
            ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = { 'Content-Type':'application/json' }
# If authentication is enabled, set the authorization header
headers['Authorization']=f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers = headers)
print(resp.text)

```

Возвращаемые результаты аналогичны приведенному ниже документу JSON.

```JSON
[217.67978776218715, 224.78937091757172]
```
