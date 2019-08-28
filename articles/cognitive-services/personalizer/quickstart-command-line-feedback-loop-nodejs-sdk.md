---
title: Краткое руководство. Клиентская библиотека Персонализатора для Node.js | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Приступите к работе с клиентской библиотекой Персонализатора для Node.js, используя цикл обучения.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 08/13/2019
ms.author: diberry
ms.openlocfilehash: fbd86698438e09a0c94c06e0e79800b8d38b57c1
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69544800"
---
# <a name="quickstart-personalize-client-library-for-nodejs"></a>Краткое руководство. Клиентская библиотека Персонализатора для Node.js

Отобразите персонализированное содержимое с помощью службы "Персонализатор" благодаря этому краткому руководству для Node.js.

Приступите к работе с клиентской библиотекой Персонализатора для Node.js. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.

 * составление списка действий для персонализации;
 * создание отчетов об оценке вознаграждения, указывающей на успешное выполнение действия с высшим рейтингом.

[Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [Пакет (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [Примеры](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [Node.js](https://nodejs.org).

## <a name="setting-up"></a>Настройка

### <a name="create-a-personalizer-azure-resource"></a>Создание ресурса Azure для Персонализатора

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс для Персонализатора с помощью [портала Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) или [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) на локальном компьютере. Также можно:

* получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services) на 7 дней. После регистрации он будет доступен на [веб-сайте Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Просмотреть этот ресурс на [портале Azure](https://portal.azure.com/).

После получения ключа из своего ресурса или пробной подписки создайте две [переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY` — для ключа ресурса.
* `PERSONALIZER_ENDPOINT` — для конечной точки ресурса.

### <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него. 

```console
mkdir myapp && cd myapp
```

Выполните команду `npm init -y`, чтобы создать файл `package.json`. 

```console
npm init -y
```

### <a name="install-the-nodejs-library-for-personalizer"></a>Установка библиотеки Node.js для Персонализатора

Установите клиентскую библиотеку Персонализатора для Node.js с помощью следующей команды:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Установите остальные пакеты NPM для этого краткого руководства:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

### <a name="change-the-model-update-frequency"></a>Изменение частоты обновления модели

В ресурсе "Персонализатор" на портале Azure установите для параметра **Частота обновления модели** значение 10 секунд. С этим значением служба будет обучаться быстро, и вы увидите, как главное действие меняется с каждой итерацией.

![Изменение частоты обновления модели](./media/settings/configure-model-update-frequency-settings.png)

После создания цикла Персонализатора модель не используется, поскольку отсутствуют вызовы API вознаграждения для обучения. Вызовы ранжирования будут возвращать равновероятностный результат для каждого элемента. Приложение должно по-прежнему ранжировать содержимое на основе выходных данных RewardActionId.

## <a name="object-model"></a>Объектная модель

Клиент Персонализатора — это объект PersonalizerClient, который проходит проверку подлинности в Azure с помощью объекта Microsoft.Rest.ServiceClientCredentials, который содержит ваш ключ.

Чтобы запросить ранжирование содержимого, создайте RankRequest, а затем передайте его в метод client.Rank. Метод ранжирования возвращает RankResponse, содержащий ранжированное содержимое. 

Чтобы отправить результат в Персонализатор, создайте RewardRequest, а затем передайте его в метод client.Reward. 

Определение вознаграждения в этом кратком руководстве тривиально. Со временем определение факторов влияния на [оценку вознаграждения](concept-rewards.md) и сложность процесса в рабочей системе может измениться. Это должно быть одним из основных проектных решений в вашей архитектуре Персонализатора. 

## <a name="code-examples"></a>Примеры кода

Фрагменты кода по приведенным ниже ссылкам показывают, как выполнить одноименные действия с помощью клиентской библиотеки Персонализатора для Node.js:

* [создание клиента Персонализатора](#create-a-personalizer-client);
* [запрос ранжирования](#request-a-rank);
* [отправка вознаграждения](#send-a-reward).

## <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

Создайте приложение Node.js в предпочитаемом редакторе или интегрированной среде разработки, которая называется `sample.js`. 

## <a name="add-the-dependencies"></a>Добавление зависимостей

Откройте файл **sample.js** в предпочитаемом редакторе или интегрированной среде разработки. Добавьте следующие инструкции `requires` для добавления пакетов NPM:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Добавление сведений о ресурсе Персонализатора

Создайте переменные для ключа Azure и конечной точки вашего ресурса, извлеченных из переменных среды с именами `PERSONALIZER_KEY` и `PERSONALIZER_ENDPOINT`. Если вы создали переменные среды после запуска приложения, то для доступа к переменной следует закрыть и перезагрузить редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена. Методы будут созданы позже в этом руководстве.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Создание клиента Персонализатора

Теперь создайте метод для возврата клиента Персонализатора. Параметр метода — `PERSONALIZER_RESOURCE_ENDPOINT`, а apiKey — `PERSONALIZER_RESOURCE_KEY`.

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Получение вариантов содержимого, представленных в виде действий

Действия представляют выборы содержимого, которые Персонализатор должен ранжировать. Добавьте следующие методы в класс Program, чтобы получить из командной строки пользовательские входные данные о времени суток и текущем предпочтении в пище.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Создание цикла обучения

Цикл обучения персонализации — это цикл вызовов [ранжирования](#request-a-rank) и [вознаграждений](#send-a-reward). В этом кратком руководстве за каждым вызовом ранжирования для персонализации содержимого следует вызов вознаграждения. Это необходимо, чтобы определить, насколько хорошо служба оценивает содержимое. 

Следующий код циклически запрашивает у пользователя предпочтения в командной строке, отправляя эти сведения в Персонализатор для ранжирования. Затем он предоставляет список вариантов клиенту для выбора и отправляет вознаграждения в Персонализатор, указывая, насколько хорошо служба выполнила ранжирование выбора.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

В следующих разделах мы подробнее рассмотрим вызовы ранжирования и вознаграждений.

## <a name="request-a-rank"></a>Запрос ранжирования

Чтобы выполнить запрос ранжирования, программа запрашивает предпочтения пользователя для создания вариантов содержимого. Процесс может создавать содержимое для исключения из рейтинга. Это содержимое отображается в виде `excludeActions`. Запрос ранжирования требует [действий](concepts-features.md#actions-represent-a-list-of-options), currentContext, excludeActions и уникального идентификатора события ранжирования (такого как GUID), чтобы получить ранжированный ответ. 

В этом кратком руководстве используются простые контекстные признаки времени суток и предпочтений пользователя в пище. В рабочих системах определение и [оценка](concept-feature-evaluation.md) [действий и функций](concepts-features.md) может отличаться.  

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Отправка вознаграждения

Чтобы выполнить запрос вознаграждения, программа получает выбор пользователя из командной строки, присваивает числовое значение каждому выбору, а затем отправляет уникальный идентификатор события ранжирования и числовое значение в метод вознаграждения.

В этом кратком руководстве в качестве вознаграждения используется простое число: ноль или 1. В рабочих системах определение времени и содержимого ответа на вызов [вознаграждения](concept-rewards.md) может быть нетривиальным. Это зависит от конкретных потребностей. 

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Запуск программы

Запустите приложение с Node.js из каталога приложения.

```console
node sample.js
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
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js).
