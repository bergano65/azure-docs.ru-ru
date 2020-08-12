---
title: Краткое руководство. Создание эскиза — REST, cURL
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как создать эскиз изображения с помощью API компьютерного зрения в cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 1c2ab3f0ef84007d8b9233c45d39fcfbacb5eaab
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835312"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Краткое руководство. Создание эскиза с помощью REST API "Компьютерное зрение" и cURL

В этом кратком руководстве описано, как создать эскиз изображения с помощью REST API Компьютерного зрения. Вы можете указать желаемую высоту и ширину, которые могут отличаться пропорциями от входного изображения. API Компьютерного зрения использует интеллектуальную обрезку для идентификации интересующей области и создания координат обрезки вокруг этой области.

## <a name="prerequisites"></a>Предварительные требования

* подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/). 
* [cURL](https://curl.haxx.se/)
* Получив подписку Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="создайте ресурс Компьютерного зрения"  target="_blank">create a Computer Vision resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания щелкните **Перейти к ресурсам**.
  * Для подключения приложения к API Компьютерного зрения потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
  * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.

## <a name="create-and-run-the-sample-command"></a>Создание и запуск примера команды

Чтобы создать и запустить пример, сделайте следующее.

1. Скопируйте приведенную ниже команду в текстовый редактор.
1. При необходимости внесите следующие изменения в команду.
    1. Замените значение `<subscriptionKey>` своим ключом подписки.
    1. Замените значение `<thumbnailFile>` путем и именем файла, в котором следует сохранить эскиз.
    1. Замените первую часть URL-адреса запроса (`westcentralus`) текстом из URL-адреса своей конечной точки.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. При необходимости замените URL-адрес изображения в тексте запроса (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) на URL-адрес другого изображения для создания эскиза.
1. Откройте окно командной строки.
1. Вставьте команду из текстового редактора в окно командной строки.
1. Нажмите клавишу ВВОД, чтобы выполнить эту программу.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Изучите ответ.

При успешном получении ответа файл эскиза изображения будет записан в файл, указанный в `<thumbnailFile>`. Если запрос завершается сбоем, ответ будет содержать код ошибки и сообщение с описанием проблемы. Если запрос вроде бы выполняется, но созданный эскиз не является допустимым файлом изображения, проблема может быть в том, что ключ подписки не является допустимым.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с API Компьютерного зрения, чтобы проанализировать изображение, обнаружить знаменитостей и достопримечательности, создать эскиз, а также извлечь печатный и рукописный текст. Для быстрых экспериментов с API компьютерного зрения можно использовать [открытую консоль тестирования API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c).

> [!div class="nextstepaction"]
> [Обзор API компьютерного зрения](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f21b)
