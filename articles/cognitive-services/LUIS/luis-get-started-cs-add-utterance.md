---
title: Изменение и обучение приложения с помощью C# — LUIS
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как добавить примеры фраз в приложение Home Automation и выполнить обучение этого приложения на C#.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 55251a553921e947e7c6522677e51e4eb1d3fa5b
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619953"
---
# <a name="quickstart-change-model-using-c"></a>Краткое руководство. Изменение модели с помощью C#

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Последний [**выпуск Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Установленный язык программирования C#.
* Пакеты NuGet [JsonFormatterPlus](https://www.nuget.org/packages/JsonFormatterPlus) и [CommandLine](https://www.nuget.org/packages/CommandLineParser/).

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Файл JSON с примерами высказываний.

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Создание простого примера кода 

В Visual Studio создайте **классическое консольное приложение для Windows** на платформе .NET Framework. Присвойте проекту имя `ConsoleApp1`.

![Тип проекта Visual Studio](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

### <a name="add-the-systemweb-dependency"></a>Добавление зависимости System.Web

Проекту Visual Studio требуется зависимость **System.Web**. В обозревателе решений щелкните правой кнопкой мыши **Ссылки** и выберите **Добавить ссылку** в разделе "Сборки".

![Добавление ссылки на System.web](./media/luis-quickstart-cs-add-utterance/system.web.png)

### <a name="add-other-dependencies"></a>Добавление других зависимостей

В проект Visual Studio нужно добавить **JsonFormatterPlus** и **CommandLineParser**. В обозревателе решений щелкните правой кнопкой мыши **Ссылки** и выберите **Управление пакетами NuGet**. Найдите и добавьте оба пакета. 

![Добавление зависимостей от сторонних разработчиков](./media/luis-quickstart-cs-add-utterance/add-dependencies.png)


### <a name="write-the-c-code"></a>Написание кода C#
Файл **Program.cs** должен выглядеть так:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Обновите зависимости таким образом:

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=1-11 "Add the dependencies")]


Добавьте идентификаторы LUIS и строки в класс **Program**.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=19-30&dedent=8 "Add the LUIS IDs and strings")]

Добавьте класс для управления параметрами командной строки в класс **Program**.

   [!code-csharp[Add class to manage command line parameters.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=32-46 "Add class to manage command-line parameters.")]

Добавьте метод запроса GET в класс **Program**.

   [!code-csharp[Add the GET request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=49-59 "Add the GET request.")]


Добавьте метод запроса POST в класс **Program**. 

   [!code-csharp[Add the POST request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=60-76 "Add the POST request.")]

Добавьте примеры высказываний из файла метода в класс **Program**.

   [!code-csharp[Add example utterances from file.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=77-86 "Add example utterances from file.")]

Применив к модели все эти изменения, обучите ее. Добавьте метод в класс **Program**.

   [!code-csharp[After the changes are applied to the model, train the model.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=87-96 "After the changes are applied to the model, train the model.")]

Обучение может занять некоторое время. Проверьте состояние, чтобы узнать о его завершении. Добавьте метод в класс **Program**.

   [!code-csharp[Training may not complete immediately, check status to verify training is complete.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=97-103 "Training may not complete immediately, check status to verify training is complete.")]

Чтобы управлять аргументами командной строки, добавьте основной код. Добавьте метод в класс **Program**.

   [!code-csharp[To manage command line arguments, add the main code.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=104-137 "To manage command-line arguments, add the main code.")]

### <a name="copy-utterancesjson-to-output-directory"></a>Копирование файла utterances.json в выходной каталог

В обозревателе решений добавьте файл `utterances.json`: щелкните правой кнопкой мыши имя проекта в обозревателе решений, выберите **Добавить** и **Существующий элемент**. Выберите файл `utterances.json`. Файл будет добавлен в проект. Затем его нужно добавить в выходной каталог. Щелкните правой кнопкой мыши файл `utterances.json` и выберите **Свойства**. В окне свойств параметр **Действие при построении** должен иметь значение `Content`, а параметр **Копировать в выходной каталог** — значение `Copy Always`.  

![Пометка файла JSON как содержимого](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="build-code"></a>Компиляция кода

Скомпилируйте код в Visual Studio. 

## <a name="run-code"></a>Выполнение кода

Запустите приложение с помощью командной строки из каталога /bin/Debug. 

```console
ConsoleApp1.exe --add utterances.json --train --status
```

Эта командная строка отображает результаты вызова API добавления фраз. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов
Когда вы закончите работу с этим руководством, удалите все созданные при работе с ним файлы. 

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Создание приложения LUIS программным способом](luis-tutorial-node-import-utterances-csv.md) 
