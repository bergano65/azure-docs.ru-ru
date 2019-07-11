---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: f9b853f0e86fc298ab35421928492ba813d85827
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444626"
---
## <a name="create-an-azure-functions-project"></a>Создание проекта Функций с помощью функции 

С помощью шаблона проекта Функции Azure в Visual Studio Code можно создать проект и затем опубликовать его в виде приложения-функции в Azure. Приложение-функция позволяет группировать функции в логические единицы для развертывания и совместного использования ресурсов, а также управления ими.

1. В Visual Studio Code нажмите клавишу F1, чтобы открыть палитру команд. В палитре команд найдите и щелкните `Azure Functions: Create new project...`.

1. Выберите расположение для рабочей области проекта и нажмите кнопку **Выбрать**.

    > [!NOTE]
    > Рассматриваемые в этой статье шаги выполняются вне рабочей области. В этом случае не нужно указывать папку проекта, которая является частью рабочей области.

1. Следуя инструкциям, введите следующие сведения:

    | prompt | Значение | ОПИСАНИЕ |
    | ------ | ----- | ----------- |
    | Select a language for your function app project (Выберите язык для проекта приложения-функции) | C# или JavaScript | В этой статье поддерживается C# и JavaScript. Для Python см. [эту статью о Python](https://code.visualstudio.com/docs/python/tutorial-azure-functions), а для PowerShell — [эту статью о PowerShell](../articles/azure-functions/functions-create-first-function-powershell.md).  |
    | Select a template for your project's first function (Выберите шаблон для первой функции вашего проекта) | Триггер HTTP | Создание функции, активируемой HTTP, в новом приложении-функции. |
    | Provide a function name (Укажите имя функции) | HTTPTrigger | Нажмите клавишу "ВВОД", чтобы использовать имя по умолчанию. |
    | Provide a namespace (Укажите пространство имен) | My.Functions | (Только для C#.) У библиотек классов C# должно быть пространство имен.  |
    | Уровень авторизации | Функция | Для вызова конечной точки HTTP функции требуется [ключ функции](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys). |
    | Select how you would like to open your project (Выберите, как вы хотели бы открыть свой проект) | Добавление в рабочую область | Создает приложение-функцию в текущей рабочей области. |

Редактор Visual Studio Code создаст проект приложения-функции в новой рабочей области. Проект будет содержать файлы конфигурации [host.json](../articles/azure-functions/functions-host-json.md) и [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file), а также файлы проекта для указанного языка. 

Новая функция, запускаемая по протоколу HTTP, также создается в папке HttpTrigger проекта приложения-функции.