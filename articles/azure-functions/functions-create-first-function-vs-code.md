---
title: Создание функции в Azure с помощью Visual Studio Code
description: Создание и публикация в Azure простой функции, активируемой по HTTP, с помощью расширения "Функции Azure" для Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 0540c7b01d693975f34515c7d13f0477ac74d4a1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842262"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Краткое руководство. Создание проекта Функций Azure в Visual Studio Code

В этой статье вы узнаете, как создать функцию, которая отвечает на HTTP-запросы, используя Visual Studio Code. После тестирования кода в локальной среде его необходимо развернуть в бессерверной среде Функций Azure. Выполнение этого краткого руководства предполагает небольшую дополнительную плату в несколько центов США в учетной записи Azure. 

Существует также версия этой статьи для [интерфейса командной строки](functions-create-first-azure-function-azure-cli.md).

## <a name="prerequisites"></a>Предварительные требования

+ [Visual Studio Code](https://code.visualstudio.com/) на одной из [поддерживаемых платформ](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 
::: zone pivot="programming-language-csharp"
+ [Расширение C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) для Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), активная версия LTS и версия Maintenance LTS (рекомендуются версии 8.11.1 и 10.14.1).
::: zone-end
::: zone pivot="programming-language-python"
+ [Python 3.7](https://www.python.org/downloads/release/python-375/) или [Python 3.6](https://www.python.org/downloads/release/python-368/), поддерживаемый Функциями Azure. Python 3.8 пока не поддерживается. 

+ [Расширение Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) для Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [Пакет SDK для .NET Core 2.2 и более поздних версий](https://www.microsoft.com/net/download).

+ [Расширение PowerShell для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). 
::: zone-end

+ [Расширение "Функции Azure"](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) для Visual Studio Code. 

+ [Учетная запись Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) с активной подпиской. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начать работу.

## <a name="create-an-azure-functions-project"></a>Создание локального проекта 

В этом разделе вы используете Visual Studio Code, чтобы создать локальный проект Функций Azure на выбранном языке. Далее в этой статье вы опубликуете код функции в Azure. 

1. Щелкните значок Azure на панели действий, а затем в области **Azure: Functions** (Azure: Функции) щелкните значок **Создать проект...** .

    ![Выбор варианта "Создать проект"](media/functions-create-first-function-vs-code/create-new-project.png)

1. Выберите расположение для рабочей области проекта и нажмите кнопку **Выбрать**.

    > [!NOTE]
    > Рассматриваемые в этой статье шаги выполняются вне рабочей области. В этом случае не нужно указывать папку проекта, которая является частью рабочей области.

1. Введите следующие сведения по соответствующим запросам:

    ::: zone pivot="programming-language-csharp"

    | prompt | Значение | 
    | ------ | ----- | 
    | Select a language for your function app project (Выберите язык для проекта приложения-функции) | C# |
    | Выбор версии | Функции Azure версии 2 | 
    | Select a template for your project's first function (Выберите шаблон для первой функции вашего проекта) | Триггер HTTP | 
    | Provide a function name (Укажите имя функции) | HttpExample | 
    | Provide a namespace (Укажите пространство имен) | My.Functions | 
    | Уровень авторизации | Анонимные | 
    | Select how you would like to open your project (Выберите, как вы хотели бы открыть свой проект) | Добавление в рабочую область |

    ::: zone-end

    ::: zone pivot="programming-language-javascript"

    | prompt | Значение | 
    | ------ | ----- | 
    | Select a language for your function app project (Выберите язык для проекта приложения-функции) | JavaScript | 
    | Выбор версии | Функции Azure версии 2 | 
    | Select a template for your project's first function (Выберите шаблон для первой функции вашего проекта) | Триггер HTTP | 
    | Provide a function name (Укажите имя функции) | HttpExample | 
    | Уровень авторизации | Анонимные | 
    | Select how you would like to open your project (Выберите, как вы хотели бы открыть свой проект) | Добавление в рабочую область |

    ::: zone-end

    ::: zone pivot="programming-language-typescript"

    | prompt | Значение | 
    | ------ | ----- | 
    | Select a language for your function app project (Выберите язык для проекта приложения-функции) | TypeScript | 
    | Выбор версии | Функции Azure версии 2 | 
    | Select a template for your project's first function (Выберите шаблон для первой функции вашего проекта) | Триггер HTTP | 
    | Provide a function name (Укажите имя функции) | HttpExample | 
    | Уровень авторизации | Анонимные | 
    | Select how you would like to open your project (Выберите, как вы хотели бы открыть свой проект) | Добавление в рабочую область |

    ::: zone-end

    ::: zone pivot="programming-language-powershell"

    | prompt | Значение | 
    | ------ | ----- | 
    | Select a language for your function app project (Выберите язык для проекта приложения-функции) | PowerShell | 
    | Выбор версии | Функции Azure версии 2 | 
    | Select a template for your project's first function (Выберите шаблон для первой функции вашего проекта) | Триггер HTTP | 
    | Provide a function name (Укажите имя функции) | HttpExample | 
    | Уровень авторизации | Анонимные | 
    | Select how you would like to open your project (Выберите, как вы хотели бы открыть свой проект) | Добавление в рабочую область |

    ::: zone-end

    ::: zone pivot="programming-language-python"

    | prompt | Значение | 
    | ------ | ----- | 
    | Select a language for your function app project (Выберите язык для проекта приложения-функции) | Python | 
    | Выбор версии | Функции Azure версии 2 | 
    | Select a Python alias to create a virtual environment (Выберите псевдоним Python для создания виртуальной среды) | Псевдоним Python | 
    | Select a template for your project's first function (Выберите шаблон для первой функции вашего проекта) | Триггер HTTP | 
    | Provide a function name (Укажите имя функции) | HttpExample | 
    | Уровень авторизации | Анонимные | 
    | Select how you would like to open your project (Выберите, как вы хотели бы открыть свой проект) | Добавление в рабочую область | 

    ::: zone-end

1. Visual Studio Code делает следующее:

    + Создает проект Функций Azure в новой рабочей области, которая содержит файлы конфигурации [host.json](functions-host-json.md) и [local.settings.json](functions-run-local.md#local-settings-file). 

    ::: zone pivot="programming-language-csharp"

    + Создает [файл библиотеки класса HttpExample.cs](functions-dotnet-class-library.md#functions-class-library-project), реализующий функцию.

    ::: zone-end
        
    ::: zone pivot="programming-language-javascript"
    
    + Создает файл package.json в корневой папке.

    + Создает папку HttpExample, содержащую файл определения [function.json](functions-reference-node.md#folder-structure), файл [index.js](functions-reference-node.md#exporting-a-function) и файл Node.js, содержащий код функции.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-typescript"
    
    + Создает файлы package.json и tsconfig.json в корневой папке.

    + Создает папку HttpExample, содержащую файл определения [function.json](functions-reference-node.md#folder-structure), файл [index.ts](functions-reference-node.md#typescript) и файл TypeScript, содержащий код функции.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-powershell"
    
    + Создает папку HttpExample, содержащую файл определения [function.json](functions-reference-python.md#programming-model) и файл run.ps1, содержащий код функции.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-python"
    
    + Создает файл requirements.txt на уровне проекта, в котором перечислены пакеты, необходимые для Функций.
    
    + Создает папку HttpExample, содержащую [файл определения function.json](functions-reference-python.md#programming-model) и файл \_\_init\_\_.py, содержащий код функции.
    
    ::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Убедившись, что функция выполняется правильно на локальном компьютере, опубликуйте проект в Azure с помощью Visual Studio Code. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Запуск функции в Azure

1. Скопируйте URL-адрес HTTP-триггера на панели **Output** (Выходные данные). Как и в предыдущем случае, добавьте строку запроса `name` в виде `?name=<yourname>` в конец этого URL-адреса и выполните запрос.

    URL-адрес для вызова функции, активируемой HTTP-запросом, должен быть указан в таком формате:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=<yourname> 

1. Вставьте этот URL-адрес HTTP-запроса в адресную строку браузера. В примере ниже показан ответ в браузере на удаленный запрос GET, возвращаемый функцией: 

    ![Ответ функции в браузере](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

При переходе к следующему шагу, [Подключение функции к службе хранилища Azure с помощью Visual Studio Code](functions-add-output-binding-storage-queue-vs-code.md), вам потребуется сохранить все ресурсы, чтобы использовать их в будущем.

В противном случае выполните следующие действия, чтобы удалить приложение-функцию и связанные с ним ресурсы и избежать дополнительных расходов.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Дополнительные сведения о затратах на использование Функций см. в статье [Estimating Consumption plan costs](functions-consumption-costs.md) (Оценка затрат на план потребления).

## <a name="next-steps"></a>Дальнейшие действия

С помощью Visual Studio Code вы создали приложение-функцию с простой функцией, активируемой HTTP-запросом. В следующей статье вы расширите эту функцию, добавив выходную привязку. Эта привязка записывает строку из HTTP-запроса в сообщение очереди Хранилища очередей Azure. 

> [!div class="nextstepaction"]
> [Connect functions to Azure Storage using Visual Studio Code](functions-add-output-binding-storage-queue-vs-code.md) (Подключение функций к службе хранилища Azure с помощью Visual Studio Code)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
