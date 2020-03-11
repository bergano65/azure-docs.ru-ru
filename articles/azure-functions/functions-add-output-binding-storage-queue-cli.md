---
title: Подключение Функций Azure к службе хранилища Azure с помощью средств командной строки
description: Сведения о подключении Функций Azure к очереди службы хранилища Azure с помощью добавления выходной привязки к проекту командной строки.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: e3c37b368b723cc95302949baa8e85e2a8b621be
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201005"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Подключение Функций Azure к службе хранилища Azure с помощью средств командной строки

Из этой статьи вы узнаете, как интегрировать очередь службы хранилища Azure с функцией и учетной записью хранения, созданными в [предыдущем кратком руководстве](functions-create-first-azure-function-azure-cli.md). Чтобы реализовать такую интеграцию, используется *выходная привязка*, которая записывает в сообщение очереди данные из HTTP-запроса. Выполнение шагов из этой статьи не влечет за собой никаких дополнительных затрат, помимо нескольких центов США, потраченных при выполнении предыдущего краткого руководства. Дополнительные сведения см. в статье [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md).

## <a name="configure-your-local-environment"></a>Настройка локальной среды

Перед началом работы необходимо выполнить действия, описанные в статье [Краткое руководство. Создание функции в Azure, которая отвечает на HTTP-запросы](functions-create-first-azure-function-azure-cli.md). Если вы уже удалили ресурсы по окончании работы по этой статье, выполните шаги еще раз, чтобы повторно создать приложение-функцию и связанные ресурсы в Azure.

## <a name="retrieve-the-azure-storage-connection-string"></a>Получение строки подключения к Службе хранилища Azure

Когда в предыдущей статье вы создавали приложение-функцию, вы также создали учетную запись хранения в Azure. Строка подключения данной учетной записи надежно хранится в параметрах приложения в Azure. Скачав параметр в файл *local.settings.json*, вы можете использовать это подключение для записи данных в очередь службы хранилища в той же учетной записи при локальном запуске функции. 

1. В корневом каталоге проекта выполните следующую команду, заменив `<APP_NAME>` именем приложения-функции из предыдущего краткого руководства. Эта команда перезапишет все существующие значения в файле.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. Откройте файл *local.settings.json* и найдите значение `AzureWebJobsStorage`, которое является строкой подключения к учетной записи хранения. В других разделах этой статьи вы будете использовать имя `AzureWebJobsStorage` и строку подключения.

> [!IMPORTANT]
> Файл *local.settings.json* содержит секреты, скачанные из Azure, поэтому всегда исключайте этот файл из системы управления версиями. Файл *.gitignore*, созданный с помощью локального проекта функций, по умолчанию исключает файл.

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## <a name="add-an-output-binding-definition-to-the-function"></a>Добавление определения выходной привязки к функции

У функции может быть только один триггер, но у нее может быть несколько входных и выходных привязок, которые позволяют подключаться к другим службам и ресурсам Azure без написания пользовательского кода интеграции. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Эти привязки объявляются в файле *function.json* в папке функции. В предыдущем кратком руководстве файл *function.json* в папке *HttpExample* содержит две привязки в коллекции `bindings`:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
У каждой привязки есть по крайней мере такие параметры, как тип, направление и имя. В примере выше у первой привязки тип `httpTrigger` и направление `in`. Для направления `in` `name` указывает имя входного параметра, который отправляется в функцию, когда ее вызывает триггер.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Вторая привязка в коллекции называется `res`. Эта привязка `http` является выходной привязкой (`out`), которая используется для записи HTTP-ответа. 

Чтобы записать в очередь службы хранилища Azure данные из этой функции, добавьте привязку `out` типа `queue` с именем `msg`, как показано в коде ниже:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
Вторая привязка в коллекции относится к типу `http` с направлением `out`. В этом случае специальный параметр `name` `$return` указывает, что эта привязка использует возвращаемое значение функции, а не предоставляет входной параметр.

Чтобы записать в очередь службы хранилища Azure данные из этой функции, добавьте привязку `out` типа `queue` с именем `msg`, как показано в коде ниже:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
Вторая привязка в коллекции называется `res`. Эта привязка `http` является выходной привязкой (`out`), которая используется для записи HTTP-ответа. 

Чтобы записать в очередь службы хранилища Azure данные из этой функции, добавьте привязку `out` типа `queue` с именем `msg`, как показано в коде ниже:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
В этом случае `msg` передается функции в качестве выходного аргумента. Для типа `queue` необходимо также указать имя очереди в `queueName` и указать *имя* подключения к службе хранилища Azure (из *local.settings.json*) в `connection`. 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

Дополнительные сведения о привязках см. в статье [Azure Functions triggers and bindings](functions-triggers-bindings.md) (Основные понятия триггеров и привязок в Функциях Azure) и в разделе о [конфигурации выходных данных очереди](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Добавление кода для использования выходной привязки

С помощью привязки очереди, указываемой в файле *function.json*, теперь можно обновлять функцию, чтобы она получала выходной параметр `msg` и записывала сообщения в очередь.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

Обратите внимание, что *не* нужно писать код для проверки подлинности, получения ссылки на очередь или записи данных. Все эти задачи интеграции удобно осуществляются в среде выполнения Функций Azure и с использованием выходной привязки очереди.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Просмотр сообщения в очереди службы хранилища Azure

Очередь можно просмотреть на [портале Azure](../storage/queues/storage-quickstart-queues-portal.md) или в [Обозревателе службы хранилища Azure](https://storageexplorer.com/). Очередь также можно просмотреть в интерфейсе командной строки Azure. Для этого выполните приведенные ниже шаги:

1. Откройте файл *local.setting.json* проекта функций и скопируйте значение строки подключения. В окне терминала или командной строки выполните следующую команду, чтобы создать переменную среды с именем `AZURE_STORAGE_CONNECTION_STRING`. Вставьте конкретную строку подключения вместо `<MY_CONNECTION_STRING>`. (Эта переменная среды означает, что вам не нужно указывать строку подключения для каждой последующей команды с помощью аргумента `--connection-string`.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Дополнительно) Команду [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) можно использовать для просмотра очередей службы хранилища в учетной записи. В выходных данных этой команды должна быть очередь с именем `outqueue`, созданная при написании функцией первого сообщения в этой очереди.
    
    ```azure-cli
    az storage queue list --output tsv
    ```

1. Используйте команду [`az storage message get`](/cli/azure/storage/message#az-storage-message-get), чтобы прочитать сообщение из этой очереди. Необходимо указывать первое имя, использованное ранее при проверке функции. Команда считывает и удаляет первое сообщение из очереди. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Этот сценарий использует certutil для декодирования коллекции сообщений в кодировке Base64 из локального временного файла. Если выходные данные отсутствуют, попробуйте удалить `> NUL` из сценария, чтобы отключить подавление выходных данных certutil, если возникает ошибка. 
    
    ---
    
    Так как текст сообщения хранится [в кодировке Base64](functions-bindings-storage-queue-trigger.md#encoding), перед отображением сообщения его необходимо декодировать. После выполнения `az storage message get` сообщение удаляется из очереди. Если в `outqueue` было только одно сообщение, вы не получите сообщение при повторном выполнении этой команды, вместо этого возникнет ошибка.

## <a name="redeploy-the-project-to-azure"></a>Повторное развертывание проекта в Azure

Теперь, когда вы локально проверили, что функция записала сообщение в очередь службы хранилища Azure, вы можете повторно развернуть проект, чтобы обновить конечную точку, работающую в Azure.

1. В папке *LocalFunctionsProj* используйте команду [`func azure functionapp publish`](functions-run-local.md#project-file-deployment), чтобы повторно развернуть проект, заменив `<APP_NAME>` именем приложения.

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
1. Как и в предыдущем кратком руководстве, используйте браузер или cURL для проверки повторно развернутой функции.

    # <a name="browser"></a>[Браузер](#tab/browser)
    
    Скопируйте полный URL-адрес вызова **Invoke URL**, показанный в выходных данных команды publish, в адресную строку браузера, добавив параметр запроса `&name=Functions`. В браузере должны отображаться выходные данные, аналогичные данным при локальном запуске функции.

    ![Выходные данные функции, выполняемой в Azure в браузере](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    Запустите [`curl`](https://curl.haxx.se/), используя **Invoke URL** и добавив параметр `&name=Functions`. Результатом выполнения этой команды должен быть текст Hello Functions.
    
    ![Выходные данные функции, выполняемой в Azure с помощью CURL](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Проверьте очередь службы хранилища еще раз, как описано в предыдущем разделе, чтобы убедиться, что в ней содержится новое сообщение, записанное в очередь.

## <a name="clean-up-resources"></a>Очистка ресурсов

После завершения работы используйте следующую команду, чтобы удалить группу ресурсов и все содержащиеся в ней ресурсы и избежать дополнительных затрат.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Дальнейшие действия

Вы обновили функцию, активируемую HTTP, которую теперь можно использовать для записи данных в очередь хранилища. Теперь вы можете узнать больше о разработке функций из командной строки с помощью Core Tools и Azure CLI:

+ [Работа с Azure Functions Core Tools](functions-run-local.md).  

::: zone pivot="programming-language-csharp"  
+ [Примеры полных проектов Функций на C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Справочник разработчика C# по функциям Azure](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Примеры полных проектов Функций на JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Руководство разработчика JavaScript для Функций Azure](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Примеры полных проектов Функций на TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Руководство разработчика TypeScript для Функций Azure](functions-reference-node.md#typescript).  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Примеры полных проектов Функций на Python](/samples/browse/?products=azure-functions&languages=python).

+ [Azure Functions Python Developer Guide](functions-reference-python.md) (Справочник по Функциям Azure для разработчика Python)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Примеры полных проектов Функций в PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Руководство разработчика PowerShell для Функций Azure](functions-reference-powershell.md). 
::: zone-end
+ [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)

+ [Страница цен на Функции Azure](https://azure.microsoft.com/pricing/details/functions/)

+ [Оценка затрат на план потребления](functions-consumption-costs.md). 
