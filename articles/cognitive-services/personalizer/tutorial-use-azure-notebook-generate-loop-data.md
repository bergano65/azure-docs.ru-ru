---
title: Руководство по Azure Notebook. Персонализатор
titleSuffix: Azure Cognitive Services
description: В этом руководстве описано, как смоделировать в Azure Notebook цикл _system Персонализатора, который предлагает типы кофе для клиентов. Пользователи и их предпочтения хранятся в наборе данных о пользователях. Также доступны сведения о видах кофе, сохраненные в наборе данных о кофе.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 7c0dc40ee2d748b1f48c3254a3e3a6e197069c08
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515170"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Руководство по Использование Персонализатора в Azure Notebook

В этом руководстве описано, как в Azure Notebook выполняется цикл Персонализатора, на примере которого мы рассмотрим соответствующий полный жизненный цикл. 

Этот цикл предлагает виды кофе, которые могут заказывать клиенты. Пользователи и их предпочтения хранятся в наборе данных о пользователях. Сведения о кофе хранятся в наборе данных о кофе.

## <a name="users-and-coffee"></a>Пользователи и кофе

Записная книжка случайным образом выбирает из набора данных пользователя, время суток и тип погоды. Сводка сведений о пользователе.

|Клиенты — компоненты контекста|Время суток|Тип погоды|
|--|--|--|
|Алиса<br>Владимир<br>Катя<br>Данил|Утро<br>День<br>Вечер|Солнечно<br>Дождь<br>Снег| 

Чтобы Персонализатор постепенно запоминал вид кофе, предпочитаемый каждым человеком, цикл _system_ использует сведения о кофе.

|Кофе — компоненты действия|Типы сущностей temperature|Источники происхождения|Типы обжарки|Органический|
|--|--|--|--|--|
|Капучино|Горячий|Кения|Dark|Органический|
|Капельный|Холодный|Бразилия|Светлый|Органический|
|Мокко со льдом|Холодный|Эфиопия|Светлый|Не органический|
|Латте|Горячий|Бразилия|Dark|Не органический|


**Задачей** цикла Персонализатора является поиск наилучшего соответствия между пользователями и видом кофе в максимальном количестве случаев. 

Исходный код для этого краткого руководства размещен в [репозитории GitHub с примерами для Персонализатора](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook).

## <a name="how-the-simulation-works"></a>Принцип работы моделирования

Вначале предложения Персонализатора будут успешными (с показателем вознаграждения 1) только в 20–30 % случаев. После некоторого количества запросов работа системы оптимизируется.

Выполните автономную оценку после первых 10 000 запросов. Это позволит Персонализатору изучить данные и создать оптимальную политику обучения. Примените новую политику обучения и снова запустите записную книжку с 2000 запросов. Теперь результаты цикла будут более точными.

## <a name="rank-and-reward-calls"></a>Оценка и вознаграждение вызовов

Для каждого из нескольких тысяч вызовов к службе Персонализатора Azure Notebook отправляет запрос оценки (**Rank**) в REST API:

* Уникальный идентификатор для события "оценка — запрос".
* Контекст — случайное сочетание пользователя, погоды и времени суток, который моделирует поведение пользователя на веб-сайте или мобильном устройстве.
* Компоненты — _все_ данные о кофе, на основе которых Персонализатор создает предложения.

Система получает ранжированные результаты выбора кофе, а затем сравнивает прогноз с известным вариантом пользователя в то же время дня и в ту же погоду. Если известный вариант совпадает с предложенным вариантом, Персонализатору возвращается параметр **Reward** со значением 1. В противном случае возвращается значение 0. 

> [!Note]
> Для этой имитации используется очень простой алгоритм вознаграждения. В реальном сценарии для определения оценки вознаграждения в этом алгоритме нужно применять для оценки бизнес-логику, возможно, с весовыми коэффициентами для разных аспектов взаимодействия с клиентом. 


## <a name="prerequisites"></a>Предварительные требования

* Учетная запись [Azure Notebook](https://notebooks.azure.com/). 
* [Ресурс Персонализатора Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). 
    * Если вы уже использовали ресурс Персонализатора, не забудьте [очистить данные](how-to-settings.md#clear-data-for-your-learning-loop) для этого ресурса на портале Azure. 
* Отправьте все файлы [этого примера](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) в проект Azure Notebook. 

Описание файлов.

* [Personalizer.ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) содержит записную книжку Jupyter для этого руководства.
* [Набор данных о пользователях](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) хранится в виде объекта JSON.
* [Набор данных о кофе](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) хранится в виде объекта JSON. 
* [Пример запроса в формате JSON](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) описывает ожидаемый формат запроса POST к API оценки.

## <a name="configure-personalizer-resource"></a>Настройка ресурса Персонализатора

На портале Azure настройте [ресурс Персонализатора](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer), указав **частоту обновления модели** в 15 секунд и **время ожидания вознаграждения** в 15 секунд. Эти параметры доступны на странице **[Параметры](how-to-settings.md#configure-service-settings-in-the-azure-portal)** . 

|Параметр|Значение|
|--|--|
|Частота обновления модели|15 секунд|
|Время ожидания вознаграждения|15 секунд|

Мы используем небольшие значения длительности, чтобы быстро отображать изменения при работе с руководством. Такие значения не следует использовать в рабочем сценарии, не проверив предварительно, подходят ли они для конкретных задач цикла Персонализатора. 

## <a name="set-up-the-azure-notebook"></a>Настройка Azure Notebook

1. Укажите для ядра значение `Python 3.6`. 
1. Откройте файл `Personalizer.ipynb` .

## <a name="run-notebook-cells"></a>Выполнение ячеек записной книжки

Запустите каждую из исполняемых ячеек и дождитесь завершения работы. Когда работа ячейки завершается, в скобках рядом с ней вместо `*` отображается числовое значение. В следующих разделах описаны программы каждой из ячеек и их ожидаемые выходные данные. 

### <a name="include-the-python-modules"></a>Включение модулей Python

Включите обязательные модули Python. Эта ячейка не имеет выходных данных.

```python
import json
import matplotlib.pyplot as plt
import random 
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Настройка ключа и имени ресурса Персонализатора

На портале Azure найдите значения ключа и конечной точки на странице **Быстрое начало** для ресурса персонализации. Измените значение `<your-resource-name>` на реальное имя ресурса Персонализатора. Измените значение `<your-resource-key>` на реальное значение ключа Персонализатора. 

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Вывод текущей даты и времени
Эта функция используется для фиксации времен начала, завершения итерационной функции и каждой итерации.

Эти ячейки не имеют выходных данных. Эта функция выводит текущую дату и время при вызове.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>Получение времени последнего обновления модели

Вызываемая функция `get_last_updated` выводит дату и время последнего изменения модели. 

Эти ячейки не имеют выходных данных. Эта функция выводит дату и время последнего обучения модели.

Функция использует метод GET REST API для [получения свойств модели](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties). 

```python
# ititialize variable for model's last modified date
modelLastModified = ""
```

```python
def get_last_updated(currentModifiedDate):
    
    print('-----checking model')
    
    # get model properties
    response = requests.get(personalization_model_properties_url, headers = headers, params = None)
    
    print(response)
    print(response.json())

    # get lastModifiedTime
    lastModifiedTime = json.dumps(response.json()["lastModifiedTime"])
    
    if (currentModifiedDate != lastModifiedTime):
        currentModifiedDate = lastModifiedTime
        print(f'-----model updated: {lastModifiedTime}')
```

### <a name="get-policy-and-service-configuration"></a>Получение конфигурации политики и службы

Эти два вызова REST позволяют проверить состояние службы.

Эти ячейки не имеют выходных данных. Эта функция выводит параметры службы при вызове.

```python
def get_service_settings():
    
    print('-----checking service settings')
    
    # get learning policy
    response = requests.get(personalization_model_policy_url, headers = headers, params = None)
    
    print(response)
    print(response.json())
    
    # get service settings
    response = requests.get(personalization_service_configuration_url, headers = headers, params = None)
    
    print(response)
    print(response.json())
```

### <a name="construct-urls-and-read-json-data-files"></a>Создание URL-адресов и чтение файлов данных JSON

Эта ячейка: 

* создает URL-адреса, которые используются в вызовах REST; 
* задает заголовок безопасности с помощью ключа ресурса Персонализатора; 
* задает случайное начальное значение для идентификатора события оценки (Rank);
* считывает данные из файлов JSON;
* вызывает метод `get_last_updated` (из выходных данных в примере удалена политика обучения);
* вызывает метод `get_service_settings`.

Ячейка выводит данные, полученные при вызове функций `get_last_updated` и `get_service_settings`.

```python
# build URLs
personalization_rank_url = personalization_base_url + "personalizer/v1.0/rank"
personalization_reward_url = personalization_base_url + "personalizer/v1.0/events/" #add "{eventId}/reward"
personalization_model_properties_url = personalization_base_url + "personalizer/v1.0/model/properties"
personalization_model_policy_url = personalization_base_url + "personalizer/v1.0/configurations/policy"
personalization_service_configuration_url = personalization_base_url + "personalizer/v1.0/configurations/service"

headers = {'Ocp-Apim-Subscription-Key' : resource_key, 'Content-Type': 'application/json'}

# context
users = "users.json"

# action features
coffee = "coffee.json"

# empty JSON for Rank request
requestpath = "example-rankrequest.json"

# initialize random
random.seed(time.time())

userpref = None 
rankactionsjsonobj = None 
actionfeaturesobj = None

with open(users) as handle:
    userpref = json.loads(handle.read())

with open(coffee) as handle:
    actionfeaturesobj = json.loads(handle.read())
    
with open(requestpath) as handle:
    rankactionsjsonobj = json.loads(handle.read())  
    
get_last_updated(modelLastModified)
get_service_settings()

print(f'User count {len(userpref)}')
print(f'Coffee count {len(actionfeaturesobj)}')
```

Убедитесь, что в этих выходных данных параметры `rewardWaitTime` и `modelExportFrequency` имеют значение 15 секунд. 
    
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:00:15', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:00:15', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

### <a name="troubleshooting-the-first-rest-call"></a>Устранение неполадок при первом вызове REST

Описанная выше ячейка создает первый запрос к Персонализатору. Убедитесь, что в выходных данных код состояния для вызова REST имеет значение `<Response [200]>`. Если вы получаете сообщение об ошибке (например, 404), но уверены в правильности значений ключа и имени ресурса, перезагрузите записную книжку.

Убедитесь, что количество значений для видов кофе и пользователей равно 4. При возникновении ошибки убедитесь, что вы загрузили все 3 файла JSON. 

### <a name="set-up-metric-chart-in-azure-portal"></a>Настройка схемы метрик на портале Azure

Далее в этом руководстве описан длительный процесс обработки 10 000 запросов, который отображается в браузере с обновляющимся значением в текстовом поле. Возможно, эти данные проще изучать на диаграмме или в виде итоговой суммы, которые будут доступны по завершении длительного процесса. Чтобы просмотреть эти сведения, используйте предоставляемые с ресурсом метрики. Теперь, после выполнения запроса к службе, вы можете создать диаграмму и периодически обновлять ее, пока выполняется длительный процесс.

1. Выберите ресурс "Персонализатор" на портале Azure.
1. В области навигации по ресурсам выберите **Метрики** под разделом "Мониторинг". 
1. В диаграмме щелкните **Добавить метрику**.
1. Здесь уже заполнены значения ресурса и пространства имен для метрики. Вам осталось лишь выбрать метрику **successful calls** (успешные вызовы) и агрегирование по функции **sum** (сумма).
1. Укажите для фильтра времени последние 4 часа.

    ![Настройте диаграмму метрик на портале Azure, добавив метрику для успешных вызовов за последние 4 часа.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    Теперь в диаграмме вы увидите 3 успешных вызова. 

### <a name="generate-a-unique-event-id"></a>Создание уникального идентификатора события

Эта функция создает глобально уникальный идентификатор для каждого вызова оценки. Этот идентификатор используется для идентификации сведений о вызове оценки и вознаграждения. Это значение можно получить из бизнес-процесса. Например, это может быть идентификатор веб-представления или транзакции.

Эта ячейка не имеет выходных данных. Эта функция выводит уникальный идентификатор при вызове.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Получение случайного набора из пользователя, погоды и времени суток

Эта функция выбирает уникальное сочетание пользователя, погоды и времени суток, а затем добавляет эти элементы в объект JSON для отправки в запрос оценки.

Эта ячейка не имеет выходных данных. При вызове эта функция возвращает выбранные случайным образом значения имени пользователя, погоды и времени суток.

Список содержит 4 пользователя и их предпочтения. Для краткости мы приводим его не полностью. 

```json
{
  "Alice": {
    "Sunny": {
      "Morning": "Cold brew",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Bob": {
    "Sunny": {
      "Morning": "Cappucino",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Cathy": {
    "Sunny": {
      "Morning": "Latte",
      "Afternoon": "Cold brew",
      "Evening": "Cappucino"
    }...
  },
  "Dave": {
    "Sunny": {
      "Morning": "Iced mocha",
      "Afternoon": "Iced mocha",
      "Evening": "Iced mocha"
    }...
  }
}
```

```python
def add_random_user_and_contextfeatures(namesoption, weatheropt, timeofdayopt, rankjsonobj):   
    name = namesoption[random.randint(0,3)]
    weather = weatheropt[random.randint(0,2)]
    timeofday = timeofdayopt[random.randint(0,2)]
    rankjsonobj['contextFeatures'] = [{'timeofday': timeofday, 'weather': weather, 'name': name}]
    return [name, weather, timeofday]
```


### <a name="add-all-coffee-data"></a>Добавление данных о кофе

Эта функция добавляет весь список вариантов кофе в объект JSON для отправки в запрос оценки. 

Эта ячейка не имеет выходных данных. Функция изменяет значение `rankjsonobj` при вызове.


Ниже приведен пример характеристик кофе: 

```json
{
    "id": "Cappucino",
    "features": [
    {
        "type": "hot",
        "origin": "kenya",
        "organic": "yes",
        "roast": "dark"
        
    }
}
```

```python
def add_action_features(rankjsonobj):
    rankjsonobj["actions"] = actionfeaturesobj
```

### <a name="compare-prediction-with-known-user-preference"></a>Сравнение прогнозов с известными предпочтениями пользователей

Эта функция вызывается для каждой итерации после вызова API оценки.

Она сравнивает известные предпочтения пользователя по выбору кофе в зависимости от погоды и времени суток, с предложением Персонализатора для того же пользователя по тем же параметрам. Если предложение совпадает с выбором пользователя, возвращается значение оценки 1, в противном случае — значение 0. Эта ячейка не имеет выходных данных. Эта функция выводит значение оценки при вызове.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1 
    return 0
``` 

### <a name="loop-through-calls-to-rank-and-reward"></a>Циклический перебор вызовов для оценки и вознаграждений

Следующая ячейка содержит _основной_ рабочий процесс записной книжки, который случайным образом получает пользователя и список кофе, а также отправляет эти данные в API оценки. Сравнение прогноза с известными предпочтениями пользователя с последующей отправкой вознаграждений обратно в службу Персонализатора. 

Этот цикл выполняется соответствующее количество раз: `num_requests`. При создании модели Персонализатор выполняет несколько тысяч вызовов для получения оценки и вознаграждения. 

Ниже приведен пример данных в формате JSON, которые отправляются в API оценки. Список видов кофе для краткости приводится не полностью. Полный код JSON с данными о кофе вы можете найти в `coffee.json`.

Код JSON, который отправляется в API оценки:

```json
{ 
   'contextFeatures':[ 
      { 
         'timeofday':'Evening',
         'weather':'Snowy',
         'name':'Alice'
      }
   ],
   'actions':[ 
      { 
         'id':'Cappucino',
         'features':[ 
            { 
               'type':'hot',
               'origin':'kenya',
               'organic':'yes',
               'roast':'dark'
            }
         ]
      }
        ...rest of coffee list
   ],
   'excludedActions':[ 

   ],
   'eventId':'b5c4ef3e8c434f358382b04be8963f62',
   'deferActivation':False
}
```

Ответ JSON, который возвращается из API оценки:

```json
{
    'ranking': [
        {'id': 'Latte', 'probability': 0.85 },
        {'id': 'Iced mocha', 'probability': 0.05 },
        {'id': 'Cappucino', 'probability': 0.05 },
        {'id': 'Cold brew', 'probability': 0.05 }
    ], 
    'eventId': '5001bcfe3bb542a1a238e6d18d57f2d2', 
    'rewardActionId': 'Latte'
}
```

Наконец, для каждого цикла предоставляется случайный набор пользователя, погоды и времени суток, а также вычисленное значение вознаграждения. Значение 1 обозначает, что ресурс Персонализатора правильно выбрал вид кофе для предоставленного сочетания пользователя, погоды и времени суток.

```console
1 Alice Rainy Morning Latte 1
```

Эта функция использует следующее:

* Оценка — запрос POST к REST API для [получения оценки](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank). 
* Вознаграждение — запрос POST к REST API для [передачи вознаграждения](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward).

```python
def iterations(n, modelCheck, jsonFormat):

    i = 1
    
    # default reward value - assumes failed prediction
    reward = 0

    # Print out dateTime
    currentDateTime()

    # collect results to aggregate in graph
    total = 0
    rewards = []
    count = []

    # default list of user, weather, time of day
    namesopt = ['Alice', 'Bob', 'Cathy', 'Dave']
    weatheropt = ['Sunny', 'Rainy', 'Snowy']
    timeofdayopt = ['Morning', 'Afternoon', 'Evening']
    
    
    while(i <= n):

        # create unique id to associate with an event
        eventid = add_event_id(jsonFormat)

        # generate a random sample
        [name, weather, timeofday] = add_random_user_and_contextfeatures(namesopt, weatheropt, timeofdayopt, jsonFormat)

        # add action features to rank
        add_action_features(jsonFormat) 

        # show JSON to send to Rank
        print('To: ', jsonFormat)    

        # choose an action - get prediction from Personalizer
        response = requests.post(personalization_rank_url, headers = headers, params = None, json = jsonFormat)

        # show Rank prediction 
        print ('From: ',response.json())    

        # compare personalization service recommendation with the simulated data to generate a reward value
        prediction = json.dumps(response.json()["rewardActionId"]).replace('"','')
        reward = get_reward_from_simulated_data(name, weather, timeofday, prediction)

        # show result for iteration
        print(f'   {i} {currentDateTime()} {name} {weather} {timeofday} {prediction} {reward}')

        # send the reward to the service 
        response = requests.post(personalization_reward_url + eventid + "/reward", headers = headers, params= None, json = { "value" : reward })

        # for every N rank requests, compute total correct  total
         total =  total + reward

        # every N iteration, get last updated model date and time
        if(i % modelCheck == 0):

            print("**** 10% of loop found")

            get_last_updated(modelLastModified) 

        # aggregate so chart is easier to read
        if(i % 10 == 0):
            rewards.append( total)
            count.append(i)
             total = 0

        i = i + 1
        
    # Print out dateTime
    currentDateTime()

    return [count, rewards]
```

## <a name="run-for-10000-iterations"></a>Выполнение 10 000 итераций
Выполните цикл Персонализатора 10 000 раз. Это длительный процесс. Не закрывайте браузер, пока в нем выполняется записная книжка. Время от времени обновляйте диаграмму метрик на портале Azure, чтобы отслеживать количество вызовов службы. Когда счетчик дойдет до 20 000 вызовов (вызов оценки и вызов вознаграждения для каждой итерации цикла), процесс завершится. 

```python
# max iterations
num_requests = 200

# check last mod date N% of time - currently 10%
lastModCheck = int(num_requests * .10)

jsonTemplate = rankactionsjsonobj

# main iterations
[count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
```



## <a name="chart-results-to-see-improvement"></a>Откройте диаграмму результатов, чтобы оценить улучшения 

Диаграмма создается на основе `count` и `rewards`.

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>Диаграммы выполнений для 10 000 запросов оценки

Выполните функцию `createChart`.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>Анализ данных на диаграмме

Эта диаграмма демонстрирует успешность модели для текущей политики обучения по умолчанию. 

![На диаграмме отображается успешность текущей политики обучения на протяжении выполнения теста.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


В идеале к моменту завершения теста цикл должен демонстрировать близкую к 100 % долю успешных прогнозов, без учета исследований. Значение исследования по умолчанию составляет 20 %. 

`100-20=80`

Это значение можно найти на портале Azure на странице **Параметры** для ресурса Персонализатора. 

Чтобы определить более эффективную политику обучения на основе данных, предоставленных в API оценки, выполните на портале [автономную оценку](how-to-offline-evaluation.md) для цикла Персонализатора.

## <a name="run-an-offline-evaluation"></a>Выполнение автономной оценки

1. На портале Azure откройте страницу **Оценки** для ресурса Персонализатора.
1. Щелкните **Создать оценку**.
1. Введите необходимые данные: имя оценки и диапазон дат для оценки цикла. Диапазон дат должен включать только дни, для которых вы хотите получить оценку. 
    ![На портале Azure откройте страницу "Оценки" для ресурса Персонализатора. Выберите "Создать оценку". Введите имя оценки и диапазон дат.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    Цель выполнения этой автономной оценки заключается в определении более эффективной политики обучения по тем компонентам и действиям, которые используются в этом цикле. Чтобы найти более эффективную политику обучения, не забудьте включить **политику оптимизации**.

1. Выберите **ОК**, чтобы начать процесс оценки. 
1. На странице **Оценки** отобразится новая оценка и ее текущее состояние. В зависимости от объема данных процесс оценки может занять некоторое время. Вы можете вернуться на эту страницу через несколько минут, чтобы увидеть результаты. 
1. Когда процесс завершится, выберите эту оценку и щелкните **Сравнение разных политик обучения**. Это действие отображает доступные политики обучения и их эффективность для предоставленных данных. 
1. Выберите в таблице наиболее эффективную политику обучения и щелкните **Применить**. Это действие применяет к модели _лучшую_ политику обучения и выполняет переобучение. 

## <a name="change-update-model-frequency-to-5-minutes"></a>Изменение интервала обновления модели на 5 минут

1. На портале Azure выберите страницу **Параметры** для ресурса Персонализатора. 
1. Измените значения **частоты обновления модели** и **времени ожидания вознаграждения** на 5 минут и щелкните **Сохранить**.

Изучите дополнительные сведения о [времени ожидания вознаграждения](concept-rewards.md#reward-wait-time) и [частоте обновления модели](how-to-settings.md#model-update-frequency).

```python
#Verify new learning policy and times
get_service_settings()
```

Убедитесь, что в выходных данных параметры `rewardWaitTime` и `modelExportFrequency` имеют значение 5 минут. 
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:05:00', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:05:00', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

## <a name="validate-new-learning-policy"></a>Проверка новой политики обучения 

Вернитесь к записной книжке Azure и повторите тот же цикл, но на этот раз по 2000 итераций. Время от времени обновляйте диаграмму метрик на портале Azure, чтобы отслеживать количество вызовов службы. Когда счетчик отобразит приблизительно 4000 вызовов (вызов оценки и вызов вознаграждения для каждой итерации цикла), процесс завершится. 

```python
# max iterations
num_requests = 2000

# check last mod date N% of time - currently 10%
lastModCheck2 = int(num_requests * .10)

jsonTemplate2 = rankactionsjsonobj

# main iterations
[count2, rewards2] = iterations(num_requests, lastModCheck2, jsonTemplate)
```

## <a name="run-chart-for-2000-rank-requests"></a>Диаграмма выполнений для 2000 запросов оценки

Выполните функцию `createChart`.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>Анализ второй диаграммы

Вторая диаграмма демонстрирует заметное увеличение совпадений между прогнозами оценки и предпочтениями пользователей. 

![Вторая диаграмма демонстрирует заметное увеличение совпадений между прогнозами оценки и предпочтениями пользователей.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь продолжать работу с этой серией руководств, очистите следующие ресурсы:

* Удалите проект Azure Notebook. 
* Удалите ресурс Персонализатора. 

## <a name="next-steps"></a>Дополнительная информация

[Записная книжка Jupyter и файлы данных](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook), которые используются в этом примере, можно получить в репозитории GitHub для Персонализатора. 

