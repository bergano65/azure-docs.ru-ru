---
title: включить файл
description: включить файл
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file, devx-track-javascript
ms.date: 07/30/2020
ms.openlocfilehash: ba7885859adc1d9899c66917204306c8a0d0092f
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246251"
---
[Справочная документация](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [Пакет (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [Примеры](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services).
* Текущая версия [Node.js](https://nodejs.org) и NPM.

## <a name="using-this-quickstart"></a>Использование этого краткого руководства


Для использования этого краткого руководства необходимо выполнить несколько действий.

* Создайте ресурс "Персонализатор" на портале Azure.
* На портале Azure для ресурса "Персонализатор" на странице **Конфигурация** измените частоту обновления модели на очень короткий интервал.
* В редакторе кода создайте и измените файл кода.
* В командной строке или терминале установите пакет SDK из командной строки.
* В командной строке (или терминале) запустите файл кода.

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него.

```console
mkdir myapp && cd myapp
```

Выполните команду `npm init -y`, чтобы создать файл `package.json`.

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>Установка библиотеки Node.js для Персонализатора

Установите клиентскую библиотеку Персонализатора для Node.js с помощью следующей команды:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Установите остальные пакеты NPM для этого краткого руководства:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Объектная модель

Клиент Персонализатора — это объект [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest), который проходит проверку подлинности в Azure с помощью объекта Microsoft.Rest.ServiceClientCredentials, который содержит ваш ключ.

Чтобы запросить единственный лучший элемент содержимого, создайте [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest),а затем передайте его в метод [client.Rank](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-). Метод ранжирования возвращает RankResponse.

Чтобы отправить результат в Персонализатор, создайте [RewardRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest), а затем передайте его в метод [Reward](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) в классе событий.

Определение вознаграждения в этом кратком руководстве тривиально. Со временем определение факторов влияния на [оценку вознаграждения](../concept-rewards.md) и сложность процесса в рабочей системе может измениться. Это должно быть одним из основных проектных решений в вашей архитектуре Персонализатора.

## <a name="code-examples"></a>Примеры кода

Фрагменты кода по приведенным ниже ссылкам показывают, как выполнить одноименные действия с помощью клиентской библиотеки Персонализатора для Node.js:

* [создание клиента Персонализатора](#create-a-personalizer-client);
* [API ранжирования](#request-the-best-action)
* [API вознаграждения](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

Создайте приложение Node.js в предпочитаемом редакторе или интегрированной среде разработки, которая называется `sample.js`.

## <a name="add-the-dependencies"></a>Добавление зависимостей

Откройте файл **sample.js** в предпочитаемом редакторе или интегрированной среде разработки. Добавьте следующие инструкции `requires` для добавления пакетов NPM:

[!code-javascript[Add module dependencies](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Добавление сведений о ресурсе Персонализатора

Измените переменные ключа и конечной точки в начале файла с кодом для ключа и конечной точки Azure вашего ресурса. 

[!code-javascript[Add Personalizer resource information](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Создание клиента Персонализатора

Теперь создайте метод для возврата клиента Персонализатора. Параметр метода — `PERSONALIZER_RESOURCE_ENDPOINT`, а apiKey — `PERSONALIZER_RESOURCE_KEY`.

[!code-javascript[Create a Personalizer client](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Получение вариантов содержимого, представленных в виде действий

Действия представляют варианты содержимого, из которых Персонализатор должен выбрать лучший элемент содержимого. Добавьте следующие методы в класс Program, чтобы представить набор действий и их функции.

[!code-javascript[Create user features](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Создание цикла обучения

Цикл обучения Персонализатора — это цикл вызовов [ранжирования](#request-the-best-action) и [вознаграждений](#send-a-reward). В этом кратком руководстве за каждым вызовом ранжирования для персонализации содержимого следует вызов вознаграждения. Это необходимо, чтобы определить, насколько хорошо сработала служба.

Следующий код циклически запрашивает у пользователя предпочтения в командной строке, отправляя эти сведения в Персонализатор, чтобы выбрать лучшее действие. Затем он предоставляет клиенту для выбора список вариантов и отправляет вознаграждения в Персонализатор, указывая, насколько хорошо служба выполнила свой выбор.

[!code-javascript[Create the learning loop](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=mainLoop)]

В следующих разделах мы подробнее рассмотрим вызовы ранжирования и вознаграждений.

Перед запуском файла кода добавьте следующие методы для [получения вариантов содержимого](#get-content-choices-represented-as-actions):

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>Запрос лучшего действия

Чтобы выполнить запрос "Ранжирование", программа запрашивает предпочтения пользователя для создания вариантов содержимого. Процесс может создавать содержимое для исключения из действий. Это содержимое отображается в виде `excludeActions`. Запрос "Ранжирование" требует [действий](../concepts-features.md#actions-represent-a-list-of-options) и их признаков currentContext, excludeActions и уникального идентификатора события ранжирования, чтобы получить ранжированный ответ.

В этом кратком руководстве используются простые контекстные признаки времени суток и предпочтений пользователя в пище. В рабочих системах определение и [оценка](../concept-feature-evaluation.md) [действий и функций](../concepts-features.md) может отличаться.

[!code-javascript[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=rank)]

## <a name="send-a-reward"></a>Отправка вознаграждения


Чтобы получить оценку вознаграждения для отправки в запросе вознаграждения, программа получает выбор пользователя из командной строки, присваивает числовое значение каждому выбору, а затем отправляет уникальный идентификатор события и оценку вознаграждения в виде числового значения в API вознаграждения.

В этом кратком руководстве в качестве оценки вознаграждения используется простое число: ноль или 1. В рабочих системах определение времени и содержимого ответа на вызов [вознаграждения](../concept-rewards.md) может быть нетривиальным. Это зависит от конкретных потребностей.

[!code-javascript[The Personalizer learning loop sends a reward.](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=reward)]

## <a name="run-the-program"></a>Запуск программы

Запустите приложение с Node.js из каталога приложения.

```console
node sample.js
```

![Программа быстрого запуска задает несколько вопросов, чтобы собрать настройки пользователя, известные как признаки, а затем предоставляет наиболее эффективные действия.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
