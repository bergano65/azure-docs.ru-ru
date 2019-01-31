---
title: Публикация базы знаний с использованием REST и Go
titleSuffix: QnA Maker - Azure Cognitive Services
description: В этом кратком руководстве по Go описано, как опубликовать базу знаний с использованием REST. При этом последняя версия протестированной базы знаний передается в выделенный индекс Поиска Azure, представляющий опубликованную базу знаний. Кроме того, создается конечная точка, которая может быть вызвана в приложении или чат-боте.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/19/2018
ms.author: diberry
ms.openlocfilehash: 3750615cc1010bfe67d1b93937a424009d973e70
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55209220"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Краткое руководство. Публикация базы знаний в QnA Maker с использованием Go

В этом кратком руководстве по REST описано, как программным способом опубликовать базу знаний. В процессе публикации последняя версия базы знаний отправляется в индекс выделенной службы "Поиск Azure" и создается конечная точка, которую можно вызывать в приложении или чат-боте.

В этом кратком руководстве вызываются API службы QnA Maker:
* [Публикация.](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) При использовании этого API в тексте запроса не нужно указывать какие-либо сведения.

## <a name="prerequisites"></a>Предварительные требования

* [Go 1.10.1](https://golang.org/dl/).
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить этот ключ, выберите **Ключи** в разделе **Управление ресурсами** на информационной панели. 

* Идентификатор базы знаний QnA Maker, который находится в URL-адресе в параметре строки запроса kbid, как показано ниже.

    ![Идентификатор базы знаний QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Если у вас еще нет базы знаний, ее можно создать для этого руководства. [Создание базы знаний.](create-new-kb-csharp.md)

> [!NOTE] 
> Полные файлы решения доступны в [репозитории GitHub **Azure-Samples/cognitive-services-qnamaker-go**](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-go-file"></a>Создание файла Go

Создайте в VSCode файл с именем `publish-kb.go`.

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

Чтобы добавить необходимые зависимости в проект, в верхней части файла `publish-kb.go` укажите следующие строки:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=3-7 "Add the required dependencies")]

## <a name="create-the-main-function"></a>Создание функции main

Включив необходимые зависимости, добавьте следующий класс:

```Go
package main

func main() {

}
```

## <a name="add-required-constants"></a>Добавление необходимых констант

В функции **main**


 добавьте необходимые константы для обеспечения доступа к QnA Maker. Вместо примеров подставьте собственные значения.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>Добавление запроса POST для публикации базы знаний

Включив константы, добавьте следующий код, который отправляет HTTPS-запрос к API службы QnA Maker, чтобы опубликовать базу знаний и получить ответ:

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

Вызов API возвращает состояние 204 для успешной публикации без содержимого в тексте ответа. Код добавляет содержимое для ответов 204.

Этот ответ возвращается без изменений для любого другого ответа.

## <a name="build-and-run-the-program"></a>Сборка и запуск проекта

Чтобы скомпилировать файл, выполните следующую команду: Командная строка не возвращает никаких данных в случае успешной компиляции.

```bash
go build publish-kb.go
```

В командной строке введите приведенную ниже команду, чтобы запустить программу. Запрос к API службы QnA Maker для создания базы знаний будет отправлен автоматически, а затем будет выведен результат его выполнения (код состояния 204).

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Дополнительная информация

Опубликовав базу знаний, вам нужно использовать [URL-адрес конечной точки для создания ответа](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) (Справочник по API REST QnA Maker (V4))
