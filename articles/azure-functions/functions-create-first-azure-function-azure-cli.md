---
title: Создание функции в Azure, которая отвечает на HTTP-запросы
description: Узнайте, как создать функцию в командной строке, а затем опубликовать локальный проект в бессерверном размещении в Функциях Azure.
ms.date: 03/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli, devx-track-azurepowershell
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: b7b46f2d280577f40f927a0d8eb6fcf2ed33e04a
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927436"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Краткое руководство. Создание функции в Azure, которая отвечает на HTTP-запросы

::: zone pivot="programming-language-csharp"  
Из этой статьи вы узнаете, как создать функцию класса C# на основе библиотеки, которая отвечает на HTTP-запросы, используя инструменты командной строки. После тестирования кода в локальной среде его необходимо развернуть в бессерверной среде Функций Azure. 
::: zone-end  
::: zone pivot="programming-language-javascript"
В этой статье используются средства командной строки для создания функции JavaScript, которая отвечает на HTTP-запросы. После тестирования кода в локальной среде его необходимо развернуть в бессерверной среде Функций Azure. 
::: zone-end
::: zone pivot="programming-language-typescript"
В этой статье используются средства командной строки для создания функции TypeScript, которая отвечает на HTTP-запросы. После тестирования кода в локальной среде его необходимо развернуть в бессерверной среде Функций Azure. 
::: zone-end   
::: zone pivot="programming-language-powershell"
В этой статье используются средства командной строки для создания функции PowerShell, которая отвечает на HTTP-запросы. После тестирования кода в локальной среде его необходимо развернуть в бессерверной среде Функций Azure. 
::: zone-end  
::: zone pivot="programming-language-python" 
В этой статье используются средства командной строки для создания функции Python, которая отвечает на HTTP-запросы. После тестирования кода в локальной среде его необходимо развернуть в бессерверной среде Функций Azure. 
::: zone-end  
::: zone pivot="programming-language-java" 
В этой статье используются средства командной строки для создания функции Java, которая отвечает на HTTP-запросы. После тестирования кода в локальной среде его необходимо развернуть в бессерверной среде Функций Azure. 
::: zone-end

Выполнение этого краткого руководства предполагает небольшую дополнительную плату в несколько центов США в учетной записи Azure.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Существует также версия этой статьи для [Visual Studio Code](functions-create-first-function-vs-code.md).
::: zone-end  
::: zone pivot="programming-language-java"  
> [!NOTE]
> Если вы не хотите использовать Maven в качестве средства разработки, ознакомьтесь с аналогичными руководствами для разработчиков Java по использованию [Gradle](./functions-create-first-java-gradle.md), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) и [Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-java).
::: zone-end  

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-a-local-function-project"></a>Создание локального проекта службы "Функции"

В Функциях Azure проект функций представляет собой контейнер для одной или нескольких отдельных функций, каждая из которых реагирует на конкретный триггер. Все функции в проекте совместно используют те же локальные конфигурации и конфигурации размещения. В этом разделе вы создадите проект функций, содержащий одну функцию.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Выполните команду `func init`, как показано ниже, чтобы создать проект функций в папке с именем *LocalFunctionProj* с указанной средой выполнения:  
::: zone-end  
::: zone pivot="programming-language-python"  
```
func init LocalFunctionProj --python
```
::: zone-end  
::: zone pivot="programming-language-csharp"  
```
func init LocalFunctionProj --dotnet
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```
func init LocalFunctionProj --javascript
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```
func init LocalFunctionProj --typescript
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```
func init LocalFunctionProj --powershell
```
::: zone-end    
::: zone pivot="programming-language-java"  
В пустой папке выполните следующую команду, чтобы создать проект функций из [архетипа Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html). 

> [!IMPORTANT]
> + Используйте `-DjavaVersion=11`, чтобы функции выполнялись на Java 11. Дополнительные сведения см. в разделе [Версии Java](functions-reference-java.md#java-versions). 
> + Чтобы выполнить задания из этой статьи, переменной среды `JAVA_HOME` необходимо присвоить расположение установки правильной версии JDK.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
```
---

Maven запрашивает значения, которые позволят завершить создание проекта развертывания.   
Предоставьте следующие значения в ответ на соответствующие запросы:

| prompt | Значение | Описание |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Это значение уникально идентифицирует проект среди всех остальных. Оно должно соответствовать [правилам именования пакетов](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) для Java. |
| **artifactId** | `fabrikam-functions` | Это значение содержит имя JAR-файла, без номера версии. |
| **version** | `1.0-SNAPSHOT` | Выберите значение по умолчанию. |
| **package** | `com.fabrikam` | Это значение определяет пакет Java для создаваемого кода функции. Используйте значение по умолчанию. |

Введите `Y` или нажмите клавишу ВВОД для подтверждения.

Maven создаст файлы проекта в новой папке с именем _artifactId_ , то есть `fabrikam-functions` в нашем примере. 

::: zone-end  
Перейдите в папку проекта:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionProj
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
Эта папка содержит различные файлы проекта, в том числе файлы конфигурации [local.settings.json](functions-run-local.md#local-settings-file) и [host.json](functions-host-json.md). Файл *local.settings.json* может содержать секреты, скачанные из Azure, поэтому файл по умолчанию исключен из системы управления версиями в *GITIGNORE* -файле.

[!INCLUDE [functions-cli-add-function](../../includes/functions-cli-add-function.md)]

### <a name="optional-examine-the-file-contents"></a>Проверка содержимого файла (дополнительно)

При необходимости можно сразу перейти к [локальному запуску функции](#run-the-function-locally) и просмотреть содержимое файла позже.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

Файл *HttpExample.cs* содержит метод `Run`, получающий данные запроса в переменной `req` — это запрос [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest), дополненный атрибутом **HttpTriggerAttribute** , который определяет поведение триггера. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Возвращаемый объект — это [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult), который возвращает ответное сообщение в виде [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) или [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Дополнительные сведения см. в статье [Триггеры и привязки HTTP в службе "Функции Azure"](./functions-bindings-http-webhook.md?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-java"
#### <a name="functionjava"></a>Function.java
Файл *Function.java* содержит метод `run`, получающий данные запроса в переменной `request`. Это запрос [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage), дополненный заметкой [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger), которая определяет поведение триггера. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Сообщение-ответ создается API [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder).

#### <a name="pomxml"></a>pom.xml

Параметры ресурсов Azure, созданных для размещения приложения, определяются в элементе **конфигурации** подключаемого модуля с **groupId** `com.microsoft.azure` в созданном файле pom.xml. Например, элемент конфигурации ниже указывает развертыванию на основе Maven, что приложение-функцию необходимо создать в группе ресурсов `java-functions-group` в области `westus`. Само приложение-функция работает в Windows, размещенном в плане `java-functions-app-service-plan`, который по умолчанию является бессерверным планом потребления.    

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-102":::

Вы можете изменить эти параметры, чтобы управлять созданием ресурсов в Azure, например, изменив `runtime.os` с `windows` на `linux` перед первоначальным развертыванием. Полный список параметров, поддерживаемых подключаемым модулем Maven, см. в [сведениях о конфигурации](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

#### <a name="functiontestjava"></a>FunctionTest.java

Архетип также создает модульный тест для функции. При изменении функции для добавления привязок или добавления новых функций в проект необходимо также изменить тесты в файле *FunctionTest.java*.
::: zone-end  
::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* содержит функцию Python `main()`, которая активируется в соответствии с конфигурацией в *function.json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Для триггера HTTP функция получает данные запроса в переменной `req`, как определено в файле *function.json*. `req` — это экземпляр [класса azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Возвращаемый объект, определенный как `$return` в файле *function.json* , — это экземпляр класса [azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Дополнительные сведения см. в статье [Триггеры и привязки HTTP в службе "Функции Azure"](./functions-bindings-http-webhook.md?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>Файл index.js

Файл *index.js* экспортирует функцию, которая активируется в соответствии с конфигурацией в *function.json*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

Для триггера HTTP функция получает данные запроса в переменной `req`, как определено в файле *function.json*. Ответом является объект, определенный как `$return` в файле *function.json*. Дополнительные сведения см. в статье [Триггеры и привязки HTTP в службе "Функции Azure"](./functions-bindings-http-webhook.md?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index.ts

Файл *index.ts* экспортирует функцию, которая активируется в соответствии с конфигурацией в *function.json*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Для триггера HTTP функция получает данные запроса в переменной `req` типа **HttpRequest** , как определено в файле *function.json*. Ответом является объект, определенный как `$return` в файле *function.json*. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

Файл *run.ps1* определяет скрипт функции, который активируется в соответствии с конфигурацией в *function.json*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Для триггера HTTP функция получает данные запроса, переданные в параметр `$Request`, определенный в файле *function.json*. Возвращаемый объект, определенный как `Response` в файле *function.json* , передается в командлет `Push-OutputBinding` в качестве ответа. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.json*  — это файл конфигурации, который определяет входные и выходные данные для функции `bindings`, в том числе тип триггера. 
::: zone-end

::: zone pivot="programming-language-python"
При необходимости можно изменить `scriptFile`, чтобы вызывать другой файл Python.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Для каждой привязки требуется направление, тип и уникальное имя. В HTTP-триггере есть входная привязка типа [`httpTrigger`](functions-bindings-http-webhook-trigger.md) и выходная привязка типа [`http`](functions-bindings-http-webhook-output.md).
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"    
## <a name="create-supporting-azure-resources-for-your-function"></a>Создание вспомогательных ресурсов Azure для функции

Прежде чем развернуть код функции в Azure, необходимо создать три ресурса:

- группу ресурсов — логический контейнер связанных ресурсов;
- учетную запись хранения — для сохранения состояния и других сведений о проектах;
- приложение-функцию — среду для выполнения кода функции. Оно сопоставляется с локальным проектом функций и позволяет группировать функции в логические единицы, чтобы упростить развертывание, масштабирование и совместное использование ресурсов, а также управление ими.

Для создания этих элементов используются команды интерфейса командной строки Azure. Каждая команда предоставляет выходные данные JSON после завершения.

Войдите в Azure с помощью команды [az login](/cli/azure/reference-index#az-login), если вы еще не сделали этого:

```azurecli
az login
```
    
Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create). В следующем примере создается группа ресурсов с именем `AzureFunctionsQuickstart-rg` в регионе `westeurope`. (Группу ресурсов и ресурсы целесообразно создавать в ближайшем к вам регионе. Для этого используйте команду `az account list-locations`.)

```azurecli
az group create --name AzureFunctionsQuickstart-rg --location westeurope
```

> [!NOTE]
> Вы не можете разместить приложения Windows и Linux в одной группе ресурсов. Если у вас есть группа ресурсов `AzureFunctionsQuickstart-rg` с приложением-функцией Windows или веб-приложением, необходимо использовать другую группу ресурсов.
 
    
В группе ресурсов создайте учетную запись хранения общего назначения и регион с помощью команды [az storage account create](/cli/azure/storage/account#az-storage-account-create). В следующем примере замените `<STORAGE_NAME>` подходящим глобально уникальным именем приложения. Имена должны содержать от трех до 24 символов и только в нижнем регистре. `Standard_LRS` указывает учетную запись общего назначения, которая [поддерживается Функциями](storage-considerations.md#storage-account-requirements).

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
```

В этом кратком руководстве с учетной записи хранения будет взиматься плата лишь в несколько центов США.
    
Создайте приложение-функцию с помощью команды [az functionapp create](/cli/azure/functionapp#az-functionapp-create). В примере ниже замените `<STORAGE_NAME>` именем учетной записи, использованной на предыдущем шаге, и замените `<APP_NAME>` на глобально уникальное имя, подходящее вам. `<APP_NAME>` также является доменом DNS по умолчанию для приложения-функции. 
::: zone-end  

::: zone pivot="programming-language-python"  
Если вы используете Python 3.8, измените `--runtime-version` на `3.8` и `--functions_version` на `3`.

Если вы используете Python 3.6, измените `--runtime-version` на `3.6`.

```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Если вы используете Node.js 8, также измените `--runtime-version` на `8`.


```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-csharp"  
```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-powershell"  
```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Эта команда создает приложение-функцию, работающее в указанной языковой среде выполнения в рамках [плана использования Функций Azure](functions-scale.md#consumption-plan), который не предусматривает плату за объем, используемый здесь. Эта команда также подготавливает связанный экземпляр Application Insights Azure в той же группе ресурсов. Его можно использовать для мониторинга приложения-функции и просмотра журналов. Дополнительные сведения см. в разделе [Мониторинг функций Azure](functions-monitoring.md). Этот экземпляр не создает затраты, пока вы не активируете его.
    
## <a name="deploy-the-function-project-to-azure"></a>Развертывание проекта функций в Azure
::: zone-end  

::: zone pivot="programming-language-typescript"  
Прежде чем использовать Core Tools для развертывания проекта в Azure, создайте готовую к работе сборку файлов JavaScript из исходных файлов TypeScript.

Следующая команда подготавливает проект TypeScript к развертыванию:

```
npm run build:production 
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Теперь, когда необходимые ресурсы готовы, вы можете развернуть проект локальных функций в приложении-функции в Azure с помощью команды [func azure functionapp publish](functions-run-local.md#project-file-deployment). В следующем примере замените `<APP_NAME>` на имя своего приложения.

```
func azure functionapp publish <APP_NAME>
```

Если отображается сообщение об ошибке Can't find app with name... (Не удалось найти приложение с именем...), подождите несколько секунд и повторите попытку. Возможно, в Azure не полностью инициализировано приложение после выполнения предыдущей команды `az functionapp create`.

Команда publish показывает результаты, аналогичные приведенным ниже (усечены для простоты):

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
</pre>

::: zone-end  
::: zone pivot="programming-language-java"  
## <a name="deploy-the-function-project-to-azure"></a>Развертывание проекта функций в Azure

Приложение-функция и связанные ресурсы создаются в Azure при первом развертывании проекта функции. Параметры ресурсов Azure, созданных для размещения приложения, определяются в файле [pom.xml](#pomxml). В этой статье вы примете значения по умолчанию.

> [!TIP]
> Чтобы создать приложение-функцию, работающее в Linux вместо Windows, измените элемент `runtime.os` в файле pom.xml с `windows` на `linux`. Работа Linux в плане потребления поддерживается в [этих регионах](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions). Приложения, работающие в Linux, и приложения, работающие под управлением ОС Windows, не могут находиться в одной группе ресурсов.

Перед развертыванием выполните команду [az login](/cli/azure/authenticate-azure-cli) в Azure CLI, чтобы войти в подписку Azure. 

```azurecli
az login
```

Используйте следующую команду, чтобы развернуть проект в виде нового приложения-функции. 

```
mvn azure-functions:deploy
```

Это создает в Azure такие ресурсы:

+ группа ресурсов; С именем _java-functions-group_.
+ учетная запись хранения; Требуется для Функций Azure. Это имя создается случайным образом на основе требований к именованию учетных записей хранения.
+ План размещения. Бессерверное размещение для приложения-функции в регионе, который указан в параметре _westus_. Имя _java-functions-app-service-plan_.
+ Приложение-функция. Приложение-функция представляет собой минимальную единицу развертывания и выполнения для ваших функций. Имя создается случайным образом на основе _artifactId_ , к которому добавляется случайное число. 

Развертывание упаковывает файлы проекта и развертывает их в новом приложении-функции [из ZIP-файла](functions-deployment-technologies.md#zip-deploy) Код выполняется из пакета развертывания в Azure.
::: zone-end

## <a name="invoke-the-function-on-azure"></a>Вызов функции в Azure

Функция использует триггер HTTP, поэтому ее необходимо вызывать через HTTP-запрос по URL-адресу в браузере или с помощью такого средства, как cURL. Во всех экземплярах параметр URL-адреса `code` является уникальным [ключом функции](functions-bindings-http-webhook-trigger.md#authorization-keys), который разрешает вызов конечной точки функции.

# <a name="browser"></a>[Браузер](#tab/browser)

Скопируйте полный URL-адрес вызова **Invoke URL** , показанный в выходных данных команды publish, в адресную строку браузера, добавив параметр запроса `&name=Functions`. В браузере должны отображаться выходные данные, аналогичные данным при локальном запуске функции.

![Выходные данные функции, выполняемой в Azure в браузере](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[curl](#tab/curl)

Запустите [`curl`](https://curl.haxx.se/), используя **Invoke URL** и добавив параметр `&name=Functions`. Результатом выполнения этой команды должен быть текст Hello Functions.

![Выходные данные функции, выполненной в Azure в cURL](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Чтобы просматривать журналы для опубликованного приложения-функции, которые ведутся практически в режиме реального времени, используйте [Live Metrics Stream для Application Insights](functions-monitoring.md#streaming-logs).
>
> Выполните следующую команду, чтобы открыть Live Metrics Stream в браузере.
>   ```
>   func azure functionapp logstream <APP_NAME> --browser
>   ```

## <a name="clean-up-resources"></a>Очистка ресурсов

При переходе к следующему шагу, [Добавление выходной привязки очереди службы хранилища Azure](functions-add-output-binding-storage-queue-cli.md), вам потребуется сохранить все ресурсы, чтобы использовать их в будущем.

В противном случае используйте следующую команду, чтобы удалить группу ресурсов и все содержащиеся в ней ресурсы и избежать дополнительных расходов.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end
::: zone pivot="programming-language-java"
```azurecli
az group delete --name java-functions-group
```
::: zone-end
::: zone pivot="programming-language-python"
Чтобы выйти из виртуальной среды, выполните команду `deactivate`.
::: zone-end

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Подключение Функций Azure к службе хранилища Azure с помощью средств командной строки](functions-add-output-binding-storage-queue-cli.md)
 
