---
title: Пример Идентификация лиц на изображениях в службе "Распознавание лиц"
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как идентифицировать неизвестные лица с помощью объектов PersonGroups, заранее созданных из изображений лиц знакомых людей.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 07/02/2020
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: bae84d93d15abe8804a430dacd2f2cddf8a9aed9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919171"
---
# <a name="example-identify-faces-in-images"></a>Пример Идентификация лиц на изображении

В этом руководстве показано, как идентифицировать неизвестные лица с помощью объектов PersonGroups, заранее созданных из изображений лиц знакомых людей. Примеры написаны на языке C# с использованием клиентской библиотеки службы "Распознавание лиц" в Azure Cognitive Services.

В этом примере демонстрируются следующее:

- Как создать PersonGroup. Объект PersonGroup содержит список знакомых людей.
- Как сопоставлять изображения лиц с определенными людьми. Изображения лица используются как основа для идентификации человека. Мы рекомендуем использовать четкие снимки с лицами анфас. Лучше всего подходят фотографии на документах. Оптимальный набор состоит из фотографий лица одного человека в разных позах, в одежде разных цветов или с разными прическами.

## <a name="prerequisites"></a>Предварительные требования
* Текущая версия [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/).
* Получив подписку Azure, перейдите к <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Создание ресурса Распознавания лиц"  target="_blank">созданию ресурса Распознавания лиц<span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания щелкните **Перейти к ресурсам** и скопируйте ключ.
* Несколько фотографий с лицами идентифицированных людей. [Скачайте примеры фотографий](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) Анны, Билла и Клэр.
* Серия тестовых фотографий. Фотографии могут содержать или не содержать лица идентифицированных людей. Используйте некоторые изображения по ссылке с примерами фотографий.

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-c-application"></a>Создание нового приложения C#

Создайте консольное приложение .NET Core на C# в предпочитаемой интегрированной среде разработки или редакторе. 

В окне консоли (cmd, PowerShell или Bash) выполните команду `dotnet new`, чтобы создать консольное приложение с именем `face-identify`. Эта команда создает простой проект Hello World на языке C# с одним файлом исходного кода: *Program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Измените каталог на созданную папку приложения. Чтобы создать приложение, выполните следующую команду:

```dotnetcli
dotnet build
```

Выходные данные сборки не должны содержать предупреждений или ошибок. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="step-1-authorize-the-api-call"></a>Шаг 1. Авторизация вызова API

Каждый вызов API распознавания лиц требует ключ подписки. Этот ключ может быть передан через параметр строки запроса или указан в заголовке запроса. Если вы используете клиентскую библиотеку, ключ подписки передается через конструктор класса **FaceClient**. Добавьте следующий код в метод **Main** класса *Program.cs*.
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<subscription key>"),
    new System.Net.Http.DelegatingHandler[] { });
```

## <a name="step-2-create-the-persongroup"></a>Шаг 2. Создание PersonGroup

На этом шаге объект **PersonGroup** с именем MyFriends содержит фотографии Анны, Билла и Клэр. Каждый пользователь имеет несколько зарегистрированных лиц. Лица должны распознаваться на изображениях. После выполнения всех этих шагов у вас есть **PersonGroup**, как показано на следующем изображении:

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Шаг 2.1. Назначение людей в PersonGroup
Пользователь является базовой единицей идентификации. Для пользователя может быть зарегистрировано одно или несколько известных лиц. **PersonGroup** — это совокупность людей. Каждый человек определяется в конкретной группе **PersonGroup**. Идентификация выполняется по принадлежности к **PersonGroup**. Итак, нам нужно создать объект **PersonGroup** и добавить в него изображения людей, например Анны, Билла и Клэр.

Для начала создайте **PersonGroup** с помощью API [PersonGroup — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244). Соответствующим API клиентской библиотеки является метод **CreatePersonGroupAsync** для класса **FaceClient**. Идентификатор группы, указанный при ее создании, уникален для каждой подписки. Вы можете извлекать, обновлять и удалять объекты **PersonGroup** через другие API **PersonGroup**. 

Определив группу, вы можете назначить в нее людей с помощью API [PersonGroup Person — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c). Метод клиентской библиотеки — **CreatePersonAsync**. Вы можете добавлять в PersonGroup изображения лица каждого человека из группы.

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceClient.PersonGroupPerson.CreateAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step-22-detect-faces-and-register-them-to-the-correct-person"></a><a name="step2-2"></a> Шаг 2.2. Распознавание лица и его сопоставление с определенным человеком
Обнаружение выполняется путем отправки веб-запроса POST в API [Face — Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) с файлом изображения в теле запроса HTTP. При использовании клиентской библиотеки распознавание лиц выполняется в одном из методов Detect..Asyn из класса FaceClient.

Для каждого обнаруженного лица вызовите [PersonGroup Person — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b), чтобы добавить его к соответствующему человеку.

Следующий код демонстрирует процесс определения лица с изображения и добавления его к человеку:

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Если изображение содержит несколько лиц, добавляется только лицо с наибольшим размером. Вы можете добавить несколько изображений лиц для одного человека. Передайте строку в формате "targetFace = левый край, верхний край, ширина, высота" через параметр запроса targetFace для API-интерфейса [PersonGroup Person — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b). Для добавления новых лиц также можно использовать необязательный параметр targetFace в методе AddPersonFaceAsync. Каждому добавленному лицу присваивается уникальный постоянный идентификатор лица. Вы можете использовать этот идентификатор в интерфейсах [PersonGroup Person — Delete Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) и [Face —Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Шаг 3. Обучение PersonGroup

Нужно выполнить обучение **PersonGroup**, чтобы идентификация работала правильно. После добавления человека в группу, удаления его из группы или изменения зарегистрированного для него лица необходимо повторно выполнить обучение **PersonGroup**. Обучение выполняется API-интерфейсом [PersonGroup — Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). При использовании клиентской библиотеки достаточно вызвать метод **TrainPersonGroupAsync**:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
Обучение является асинхронным процессом. Оно может продолжаться даже после возврата метода **TrainPersonGroupAsync**. Если нужно узнать текущее состояние обучения, используйте для этого API [PersonGroup — Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) или метод **GetPersonGroupTrainingStatusAsync** из клиентской библиотеки. В следующем примере демонстрируется простая логика, ожидающая завершения обучения **PersonGroup**:
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceClient.PersonGroup.GetTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != TrainingStatusType.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Шаг 4. Определение лица для определенной группы PersonGroup

При выполнении идентификаций служба "Распознавания лиц" вычисляет сходство распознаваемого лица со всеми лицами в группе. Оно возвращает людей с лицами, больше всего похожими на распознаваемое лицо. Этот процесс осуществляется через API [Face — Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) или метод клиентской библиотеки IdentifyAsync.

Распознаваемое лицо должно определяться на предыдущих шагах. Идентификатор лица передается в API идентификации в качестве второго аргумента. Можно определить несколько идентификаторов лица за раз. Результат будет содержать все выявленные результаты. По умолчанию процесс идентификации возвращает только одного человека, которому лучше всего соответствует распознаваемое лицо. Вы можете указать необязательный параметр _maxNumOfCandidatesReturned_, чтобы процесс идентификации возвращал больше кандидатов.

Этот процесс идентификации показан в следующем коде:

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceClient.Face.DetectWithStreamAsync(s);
    var faceIds = faces.Select(face => face.FaceId.Value).ToArray();
 
    var results = await faceClient.Face.IdentifyAsync(faceIds, personGroupId);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceClient.PersonGroupPerson.GetAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Когда вы завершите шаги, попробуйте распознать разные лица. Выясните, удается ли правильно опознать лица Анны, Билла или Клэр по изображениям, загруженным для обнаружения лиц. См. следующие примеры.

![Идентификация разных лиц](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Шаг 5. Запрос большого объема

**PersonGroup** может содержать не более 10 000 человек из-за прежних конструктивных ограничений. Дополнительную информацию о миллионных сценариях см. в статье [How to use the large-scale feature](how-to-use-large-scale.md) (Как использовать функцию увеличения масштаба).

## <a name="summary"></a>Сводка

В этом руководстве вы изучили процесс создания **PersonGroup** и идентификации человека. Мы объяснили и продемонстрировали следующие возможности:

- обнаружение лиц с помощью API [Face — Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d);
- создание объектов PersonGroup с помощью API [PersonGroup — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244);
- назначение людей в PersonGroup с помощью API [Person — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c);
- обучение PersonGroup с помощью API [PersonGroup — Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249);
- идентификация неизвестных лиц по лицам из PersonGroup с помощью API [Face — Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="related-topics"></a>Связанные разделы

- [Концепции распознавания лиц](../concepts/face-recognition.md)
- [Определение лиц на изображении](HowtoDetectFacesinImage.md)
- [Добавление лиц](how-to-add-faces.md)
- [Использование функции увеличения масштаба](how-to-use-large-scale.md)
