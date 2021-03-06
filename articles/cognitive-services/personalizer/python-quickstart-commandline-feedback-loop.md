---
title: Краткое руководство. Клиентская библиотека Персонализатора для Python | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Приступите к работе с клиентской библиотекой Персонализатора для Python, используя цикл обучения.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 02c4e0142ed7b3719cc07306f089769c532d6653
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494429"
---
# <a name="quickstart-personalizer-client-library-for-python"></a>Краткое руководство. Клиентская библиотека Персонализатора для Python

Отобразите персонализированное содержимое с помощью службы "Персонализатор" благодаря этому краткому руководству для Python.

Приступите к работе с клиентской библиотекой Персонализатора для Python. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.

 * составление списка действий для персонализации;
 * создание отчетов об оценке вознаграждения, указывающей на успешное выполнение действия с высшим рейтингом.

[Пакеты (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/)) | [Примеры](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Использование этого краткого руководства


Для использования этого краткого руководства необходимо выполнить несколько действий.

* Создайте ресурс "Персонализатор" на портале Azure.
* На портале Azure в ресурсе "Персонализатор" на странице **Настройка** измените частоту обновления модели.
* В редакторе кода создайте и измените файл кода.
* В командной строке или терминале установите пакет SDK из командной строки.
* В командной строке (или терминале) запустите файл кода.


## <a name="create-a-personalizer-azure-resource"></a>Создание ресурса Azure для Персонализатора

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс для Персонализатора с помощью [портала Azure](https://portal.azure.com/) или [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) на локальном компьютере. Дополнительные сведения см. в статье о [создании ресурса Cognitive Services с помощью портал Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Также можно:

* получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services) на 7 дней. После регистрации он будет доступен на [веб-сайте Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Просмотреть этот ресурс на [портале Azure](https://portal.azure.com/).

После получения ключа из своего ресурса или пробной подписки создайте две [переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY` — для ключа ресурса.
* `PERSONALIZER_ENDPOINT` — для конечной точки ресурса.

На портале Azure ключ и значения конечных точек можно найти на странице **Быстрый запуск**.


## <a name="install-the-python-library-for-personalizer"></a>Установка библиотеки Python для персонализации

Установите клиентскую библиотеку Персонализатора для Python с помощью следующей команды:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="change-the-model-update-frequency"></a>Изменение частоты обновления модели

На портале Azure в ресурсе "Персонализатор" на странице **Настройка** установите значение для параметра **Частота обновления модели** на 10 секунд. С этим значением служба будет обучаться быстро, и вы увидите, как главное действие меняется с каждой итерацией.

![Изменение частоты обновления модели](./media/settings/configure-model-update-frequency-settings.png)

После создания цикла Персонализатора модель не используется, поскольку отсутствуют вызовы API вознаграждения для обучения. Вызовы ранжирования будут возвращать равновероятностный результат для каждого элемента. Приложение должно по-прежнему ранжировать содержимое на основе выходных данных RewardActionId.

## <a name="object-model"></a>Объектная модель

Клиент Персонализатора — это объект PersonalizerClient, который проходит проверку подлинности в Azure с помощью объекта Microsoft.Rest.ServiceClientCredentials, который содержит ваш ключ.

Чтобы запросить ранжирование содержимого, создайте RankRequest, а затем передайте его в метод client.Rank. Метод ранжирования возвращает RankResponse, содержащий ранжированное содержимое. 

Чтобы отправить результат в Персонализатор, создайте RewardRequest, а затем передайте его в метод client.Reward. 

Определение вознаграждения в этом кратком руководстве тривиально. Со временем определение факторов влияния на [оценку вознаграждения](concept-rewards.md) и сложность процесса в рабочей системе может измениться. Это должно быть одним из основных проектных решений в вашей архитектуре Персонализатора. 

## <a name="code-examples"></a>Примеры кода

Фрагменты кода по приведенным ниже ссылкам показывают, как выполнить одноименные действия с помощью клиентской библиотеки Персонализатора для Python:

* [создание клиента Персонализатора](#create-a-personalizer-client);
* [запрос ранжирования](#request-a-rank);
* [отправка вознаграждения](#send-a-reward).

## <a name="create-a-new-python-application"></a>Создание приложения Python

Создайте приложение Python в предпочитаемом редакторе или среде интегрированной разработки, которая называется `sample.py`. 

## <a name="add-the-dependencies"></a>Добавление зависимостей

В каталоге проекта откройте файл **sample.py** в предпочитаемом редакторе или интегрированной среде разработки. Добавьте следующий код:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Добавление сведений о ресурсе Персонализатора

Создайте переменные для ключа Azure и конечной точки вашего ресурса, извлеченных из переменных среды с именами `PERSONALIZER_RESOURCE_KEY` и `PERSONALIZER_RESOURCE_ENDPOINT`. Если вы создали переменные среды после запуска приложения, то для доступа к переменной следует закрыть и перезагрузить редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена. Методы будут созданы позже в этом руководстве.

Имя ресурса является частью URL-адреса конечной точки: `https://<your-resource-name>.api.cognitive.microsoft.com/`.

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Создание клиента Персонализатора

Теперь создайте метод для возврата клиента Персонализатора. Параметр метода — `PERSONALIZER_RESOURCE_ENDPOINT`, а apiKey — `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Получение вариантов содержимого, представленных в виде действий

Действия представляют выборы содержимого, которые Персонализатор должен ранжировать. Добавьте следующие методы, чтобы получить из командной строки пользовательские входные данные о времени суток и текущем предпочтении в пище.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Создание цикла обучения

Цикл обучения персонализации — это цикл вызовов [ранжирования](#request-a-rank) и [вознаграждений](#send-a-reward). В этом кратком руководстве за каждым вызовом ранжирования для персонализации содержимого следует вызов вознаграждения. Это необходимо, чтобы определить, насколько хорошо служба оценивает содержимое. 

Следующий код циклически запрашивает у пользователя предпочтения в командной строке, отправляя эти сведения в Персонализатор для ранжирования. Затем он предоставляет клиенту для выбора список вариантов и отправляет вознаграждения в Персонализатор, указывая, насколько хорошо служба выполнила ранжирование выбора.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

В следующих разделах мы подробнее рассмотрим вызовы ранжирования и вознаграждений.

Перед запуском файла кода добавьте следующие методы для [получения вариантов содержимого](#get-content-choices-represented-as-actions):

* get_user_preference
* get_user_timeofday
* get_actions

## <a name="request-a-rank"></a>Запрос ранжирования

Чтобы выполнить запрос ранжирования, программа запрашивает предпочтения пользователя для создания `currentContent` вариантов содержимого. Процесс может создавать содержимое для исключения из рейтинга. Это содержимое отображается в виде `excludeActions`. Запрос ранжирования требует действий, currentContext, excludeActions и уникального идентификатора события ранжирования (такого как GUID), чтобы получить ранжированный ответ. 

В этом кратком руководстве используются простые контекстные признаки времени суток и предпочтений пользователя в пище. В рабочих системах определение и [оценка](concept-feature-evaluation.md) [действий и функций](concepts-features.md) может отличаться.  

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Отправка вознаграждения

Чтобы выполнить запрос вознаграждения, программа получает выбор пользователя из командной строки, присваивает числовое значение каждому выбору, а затем отправляет уникальный идентификатор события ранжирования и числовое значение в метод вознаграждения.

В этом кратком руководстве в качестве вознаграждения используется простое число: ноль или 1. В рабочих системах определение времени и содержимого ответа на вызов [вознаграждения](concept-rewards.md) может быть нетривиальным. Это зависит от конкретных потребностей. 

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Запуск программы

Запустите приложение с Python из каталога приложения.

```console
python sample.py
```

![Программа быстрого запуска задает несколько вопросов, чтобы собрать настройки пользователя, известные как признаки, а затем предоставляет наиболее эффективные действия.](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
>[Принцип работы Персонализатора](how-personalizer-works.md)

* [Что такое служба "Персонализатор"?](what-is-personalizer.md)
* [Где можно использовать Персонализатор](where-can-you-use-personalizer.md)
* [Устранение неполадок](troubleshooting.md)
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
