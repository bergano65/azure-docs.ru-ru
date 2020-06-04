---
title: Краткое руководство. Публикация базы знаний, RESTFUL, Go-QnA Maker
description: В этом кратком руководстве по началу работы с Go и REST описывается, как опубликовать базу знаний и создать конечную точку, которую можно вызвать в приложении или чат-боте.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: 4bfa523c6a42c8848f30ceb88770d3ce3fb03fae
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84342720"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Краткое руководство: публикация базы знаний в QnA Maker с использованием Go

В этом кратком руководстве по REST описано, как программным способом опубликовать базу знаний. В процессе публикации последняя версия базы знаний передается в индекс выделенной службы "Когнитивный поиск Azure" и создается конечная точка, которую можно вызывать в приложении или чат-боте.

В этом кратком руководстве вызываются API службы QnA Maker:
* [Публикация.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) При использовании этого API в тексте запроса не нужно указывать какие-либо сведения.

## <a name="prerequisites"></a>Предварительные требования

* [Go 1.10.1.](https://golang.org/dl/)
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить ключ и конечную точку (включая имя ресурса), щелкните **Быстрый запуск** для ресурса на портале Azure.

* Идентификатор базы знаний QnA Maker, который находится в URL-адресе в параметре строки запроса `kbid`, как показано ниже.

    ![Идентификатор базы знаний QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Если у вас еще нет базы знаний, создайте пример для этого краткого руководства по инструкциям из [этой статьи](create-new-kb-csharp.md).

> [!NOTE]
> Полный файл решения можно найти в [репозитории **Azure-Samples/qnamaker-Services--Go** GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

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

## <a name="next-steps"></a>Дальнейшие действия

Опубликовав базу знаний, вам нужно использовать [URL-адрес конечной точки для создания ответа](./get-answer-from-knowledge-base-go.md).

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://go.microsoft.com/fwlink/?linkid=2092179) (Справочник по API REST QnA Maker (V4))