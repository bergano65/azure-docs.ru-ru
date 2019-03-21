---
title: Использование пакета SDK службы "Речь" для C#
titleSuffix: Azure Cognitive Services
description: Служба "Речь" позволяет с помощью единого запроса получать аудио и возвращать объекты JSON для прогнозирования LUIS. В этой статье описано, как загрузить и использовать проект C# в Visual Studio, чтобы произнести фразу в микрофон и получить информацию о предсказании LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 6f775ffaf53019cc50bc38c294b4d5f40c8eca90
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58076757"
---
# <a name="integrate-speech-service-with-your-language-understanding-app"></a>Интеграция службы "Речь" с приложением службы "Распознавание речи"
[Служба распознавания речи](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) позволяет использовать один запрос для получения аудио и возврата объектов JSON с предсказаниями LUIS. В этой статье описано, как загрузить и использовать проект C# в Visual Studio, чтобы произнести фразу в микрофон и получить информацию о предсказании LUIS. В этом проекте используется уже включенный в качестве ссылки пакет [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) для распознавания речи. 

Для работы с этой статьей требуется бесплатная учетная запись для веб-сайта [LUIS][LUIS], в которую вы импортируете приложение.

## <a name="create-luis-endpoint-key"></a>Создание ключа конечной точки LUIS
На портале Azure [создайте](luis-how-to-azure-subscription.md) ключ **службы "Распознавание речи"** (LUIS). 

## <a name="import-human-resources-luis-app"></a>Импорт приложения LUIS Human Resources
Намерения и фразы для этой статьи взяты из приложения LUIS Human Resources, доступного в репозитории GitHub [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding). Скачайте файл [HumanResources.json](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources.json), сохраните его с расширением `.json`, а затем — [импортируйте](luis-how-to-start-new-app.md#import-new-app) в LUIS. 

Это приложение имеет намерения, сущности и фразы, относящиеся к кадровым ресурсам. Ниже приведены примеры фраз.

|Примеры высказываний|
|--|
|Who is John Smith's manager? (Кто менеджер Джона Смита?)|
|Who does John Smith manage? (Кто подчиняется Джону Смиту?)|
|Where is Form 123456? (Где находится форма 123456?)|
|Do I have any paid time off? (Полагается ли мне оплачиваемый отпуск?)|


## <a name="add-keyphrase-prebuilt-entity"></a>Добавление предварительно созданной сущности KeyPhrase
После импорта приложения нажмите **Сущности**, а затем **Add prebuilt entity** (Добавить предварительно созданную сущность). Добавьте сущность **KeyPhrase**. Сущность KeyPhrase извлекает запрашиваемое содержимое из фразы.

## <a name="train-and-publish-the-app"></a>Обучение и публикация приложения
1. На правой верхней панели навигации нажмите кнопку **Train** (Обучить) для обучения приложения LUIS.

2. Щелкните **Управление** на панели в правом верхнем углу, а затем выберите **Keys and endpoints** (Ключи и конечные точки) в области навигации слева. 

3. На странице **Keys and endpoints** (Ключи и конечные точки) назначьте ключ LUIS, созданный в разделе [Создание ключа конечной точки LUIS](#create-luis-endpoint-key).

   На этой странице скопируйте идентификатор приложения, регион публикации и идентификатор подписки ключа LUIS, созданного в разделе [Создание ключа конечной точки LUIS](#create-luis-endpoint-key). Необходимо изменить код, чтобы использовать эти значения далее в этой статье. 
  
   Для этого упражнения **не используйте** бесплатный начальный ключ. Здесь подойдет ключ **Распознавание речи**, созданный на портале Azure. 

   https://**Регион**.api.cognitive.microsoft.com/luis/v2.0/apps/**ИД_приложения**?subscription-key=**Ключ_LUIS**&q=


4. Опубликуйте приложение LUIS, нажав кнопку **Publish** (Опубликовать) на панели в правом верхнем углу. 

## <a name="audio-device"></a>Звуковое устройство
В этой статье используется звуковое устройство на вашем компьютере. Это может быть гарнитура с микрофоном или встроенное звуковое устройство. Чтобы ваша речь обнаруживалась на звуковом устройстве, проверьте уровни входного аудиосигнала и определите, следует ли говорить громче, чем обычно. 

## <a name="download-the-luis-sample-project"></a>Загрузка примера проекта LUIS
 Клонируйте или скачайте репозиторий [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding). Откройте [проект преобразование речи в намерения](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-speech-intent-recognition) в Visual Studio и восстановите пакеты NuGet. Файл решения VS имеет путь documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

Пакет SDK для распознавания речи уже включен в качестве ссылки. 

[![Снимок экрана Visual Studio 2017 с пакетом NuGet Microsoft.CognitiveServices.Speech](./media/luis-tutorial-speech-to-intent/nuget-package.png "Снимок экрана Visual Studio 2017 с пакетом NuGet Microsoft.CognitiveServices.Speech")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Изменение кода C#
Откройте файл `Program.cs` и измените следующие переменные:

|Имя переменной|Назначение|
|--|--|
|LUIS_assigned_endpoint_key|Соответствует значению ключа подписки в URL-адресе конечной точки со страницы публикации|
|LUIS_endpoint_key_region|Соответствует первому поддомену URL-адреса конечной точки, например `westus`|
|LUIS_app_ID|Соответствует URL-адресу конечной точки после **apps/**|

В файле `Program.cs` уже сопоставлены намерения из приложения Human Resources.

Выполните сборку и запустите приложение. 

## <a name="test-code-with-utterance"></a>Проверка кода с фразой
Скажите в микрофон: "Who are the approved dentists in Redmond?" (Какие в Редмонде есть сертифицированные стоматологи?).

[!code-console[Command line response from spoken utterance](~/samples-luis/documentation-samples/tutorial-speech-intent-recognition/console-output.txt "Command line response from spoken utterance")]

Правильное намерение, **GetEmployeeBenefits**, обнаружено с показателем точности 85 %. Сущность keyPhrase была возвращена. 

Пакет SDK для распознавания речи возвращает полный ответ LUIS. 

## <a name="clean-up-resources"></a>Очистка ресурсов
Удалите приложение LUIS HumanResources, если оно больше не нужно. Для этого выберите приложение, а затем на контекстной панели инструментов над списком щелкните **Delete** (Удалить). Во всплывающем диалоговом окне **Delete app?** (Удалить приложение?) нажмите кнопку **ОК**.

Не забудьте удалить каталог, когда вы закончите использовать пример кода.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Интеграция LUIS с бот](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
