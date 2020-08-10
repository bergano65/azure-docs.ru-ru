---
title: включить файл
description: включить файл
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 07/30/2020
ms.openlocfilehash: e17316d1a17ff36c0a0adf38148ef87c9714e355
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461154"
---
[Справочная документация](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [Пакет (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [Примеры](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Использование этого краткого руководства


Для использования этого краткого руководства необходимо выполнить несколько действий.

* Создайте ресурс "Персонализатор" на портале Azure.
* На портале Azure для ресурса "Персонализатор" на странице **Конфигурация** измените частоту обновления модели на очень короткий интервал.
* В редакторе кода создайте и измените файл кода.
* В командной строке или терминале установите пакет SDK из командной строки.
* В командной строке (или терминале) запустите файл кода.

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>Установка библиотеки Python для персонализации

Установите клиентскую библиотеку Персонализатора для Python с помощью следующей команды:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Объектная модель

Клиент Персонализатора — это объект [PersonalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python), который проходит проверку подлинности в Azure с помощью объекта Microsoft.Rest.ServiceClientCredentials, который содержит ваш ключ.

Чтобы запросить единственный лучший элемент содержимого, создайте [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python),а затем передайте его в метод client.Rank. Метод ранжирования возвращает RankResponse.

Чтобы отправить оценку вознаграждения в Персонализатор, задайте идентификатор события и оценку вознаграждения (значение), которые будут отправлены в метод [Reward](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) в классе EventOperations.

Определение вознаграждения в этом кратком руководстве тривиально. Со временем определение факторов влияния на [оценку вознаграждения](../concept-rewards.md) и сложность процесса в рабочей системе может измениться. Это должно быть одним из основных проектных решений в вашей архитектуре Персонализатора.

## <a name="code-examples"></a>Примеры кода

Фрагменты кода по приведенным ниже ссылкам показывают, как выполнить одноименные действия с помощью клиентской библиотеки Персонализатора для Python:

* [создание клиента Персонализатора](#create-a-personalizer-client);
* [API ранжирования](#request-the-best-action)
* [API вознаграждения](#send-a-reward).

## <a name="create-a-new-python-application"></a>Создание приложения Python

Создайте приложение Python в предпочитаемом редакторе или среде интегрированной разработки, которая называется `sample.py`.

## <a name="add-the-dependencies"></a>Добавление зависимостей

В каталоге проекта откройте файл **sample.py** в предпочитаемом редакторе или интегрированной среде разработки. Добавьте следующий код:

[!code-python[Add module dependencies](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Добавление сведений о ресурсе Персонализатора

Измените переменные ключа и конечной точки в начале файла с кодом для ключа и конечной точки Azure вашего ресурса. 

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Создание клиента Персонализатора

Теперь создайте метод для возврата клиента Персонализатора. Параметр метода — `PERSONALIZER_RESOURCE_ENDPOINT`, а apiKey — `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Получение вариантов содержимого, представленных в виде действий

Действия представляют варианты содержимого, из которых Персонализатор должен выбрать лучший элемент содержимого. Добавьте следующие методы в класс Program, чтобы представить набор действий и их признаки.

[!code-python[Present time out day preference to the user](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Создание цикла обучения

Цикл обучения Персонализатора — это цикл вызовов [ранжирования](#request-the-best-action) и [вознаграждений](#send-a-reward). В этом кратком руководстве за каждым вызовом ранжирования для персонализации содержимого следует вызов вознаграждения. Это необходимо, чтобы определить, насколько хорошо сработала служба.

Следующий код циклически запрашивает у пользователя предпочтения в командной строке, отправляя эти сведения в Персонализатор, чтобы выбрать лучшее действие. Затем он предоставляет клиенту для выбора список вариантов и отправляет вознаграждения в Персонализатор, указывая, насколько хорошо служба выполнила свой выбор.

[!code-python[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=mainLoop&highlight=9,10,29)]

Перед запуском файла кода добавьте следующие методы для [получения вариантов содержимого](#get-content-choices-represented-as-actions):

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>Запрос лучшего действия


Чтобы выполнить запрос ранжирования, программа запрашивает предпочтения пользователя для создания `currentContent` вариантов содержимого. Процесс может создавать содержимое для исключения из действий. Это содержимое отображается в виде `excludeActions`. Запрос ранжирования требует действий и их признаков, признаков currentContext, excludeActions и уникального идентификатора события, чтобы получить ответ.

В этом кратком руководстве используются простые контекстные признаки времени суток и предпочтений пользователя в пище. В рабочих системах определение и [оценка](../concept-feature-evaluation.md) [действий и функций](../concepts-features.md) может отличаться.

[!code-python[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=rank)]

## <a name="send-a-reward"></a>Отправка вознаграждения


Чтобы получить оценку вознаграждения для отправки в запросе вознаграждения, программа получает выбор пользователя из командной строки, присваивает числовое значение каждому выбору, а затем отправляет уникальный идентификатор события и оценку вознаграждения в виде числового значения в API вознаграждения.

В этом кратком руководстве в качестве оценки вознаграждения используется простое число: ноль или 1. В рабочих системах определение времени и содержимого ответа на вызов [вознаграждения](../concept-rewards.md) может быть нетривиальным. Это зависит от конкретных потребностей.

[!code-python[The Personalizer learning loop sends a reward.](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Запуск программы

Запустите приложение с Python из каталога приложения.

```console
python sample.py
```

![Программа быстрого запуска задает несколько вопросов, чтобы собрать настройки пользователя, известные как признаки, а затем предоставляет наиболее эффективные действия.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)