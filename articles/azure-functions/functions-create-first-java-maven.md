---
title: Использование Java и Maven или Gradle для публикации функции в Azure
description: Создание и публикация в Azure функции Java, активируемой с помощью HTTP-запросов, с помощью Maven или Gradle.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 08/10/2018
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: ad3b38a12020c56c31e03879b3fbcb9a8dda25f1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79136873"
---
# <a name="quickstart-use-java-and-mavengradle-to-create-and-publish-a-function-to-azure"></a>Краткое руководство. Создание и публикация функции в Azure с помощью Java и Maven или Gradle

В этой статье описано, как создать и опубликовать функцию Java в службе "Функции Azure" с помощью средства командной строки Maven или Gradle. После завершения этого руководства код функции будет выполняться в Azure в [бессерверном плане потребления](functions-scale.md#consumption-plan) с активацией по HTTP-запросу.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Предварительные требования

Для разработки функций с помощью Java, должны быть установлены следующие компоненты:

- [Java Developer Kit (JDK)](https://aka.ms/azure-jdks) версии 8.
- [Azure CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) 2.6.666 или более поздней версии.
::: zone pivot="java-build-tools-maven" 
- [Apache Maven](https://maven.apache.org) 3.0 или более поздней версии.
::: zone-end

::: zone pivot="java-build-tools-gradle"  
- [Gradle](https://gradle.org/) 4.10 или более поздней версии.
::: zone-end 

Вам также потребуется действующая подписка Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


> [!IMPORTANT]
> Переменной среде JAVA_HOME необходимо присвоить расположение установки JDK, чтобы завершить выполнение заданий этого краткого руководства.

## <a name="prepare-a-functions-project"></a>Подготовка проекта Функций Azure

::: zone pivot="java-build-tools-maven" 
В пустой папке выполните следующую команду, чтобы создать проект функций из [архетипа Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Если вы используете PowerShell, не забудьте заключить параметры в кавычки ("").

> [!NOTE]
> Если не удается запустить команду, проверьте, какая версия `maven-archetype-plugin` используется. Так как команда выполняется в пустом каталоге без файла `.pom`, она может пытаться использовать более старую версию плагина из расположения `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin`, если вы обновили Maven из предыдущей версии. В таком случае попробуйте удалить каталог `maven-archetype-plugin` и повторно выполнить команду.

Maven запрашивает значения, которые позволят завершить создание проекта развертывания. Предоставьте следующие значения в ответ на соответствующие запросы:

| Значение | Описание |
| ----- | ----------- |
| **groupId** | Это значение уникально идентифицирует проект среди всех остальных. Оно должно соответствовать [правилам именования пакетов](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) для Java. В примерах, приведенных в этом кратком руководстве, используется `com.fabrikam.functions`. |
| **artifactId** | Это значение содержит имя JAR-файла, без номера версии. В примерах, приведенных в этом кратком руководстве, используется `fabrikam-functions`. |
| **version** | Выберите значение по умолчанию `1.0-SNAPSHOT`. |
| **package** | Это значение определяет пакет Java для создаваемого кода функции. Используйте значение по умолчанию. В примерах, приведенных в этом кратком руководстве, используется `com.fabrikam.functions`. |
| **appName** | Глобально уникальное имя для нового приложения-функции в Azure. Сохраните значение по умолчанию, которое составляется из значения _artifactId_ и случайного числа. Запишите это значение, так как оно потребуется позднее. |
| **appRegion** | Выберите ближайший [регион](https://azure.microsoft.com/regions/) или регион рядом с другими службами, к которому получают доступ ваши функции. Значение по умолчанию — `westus`. Чтобы получить список всех регионов, выполните такую команду [Azure CLI]:<br/>`az account list-locations --query '[].{Name:name}' -o tsv` |
| **resourceGroup** | Имя для новой [группы ресурсов](../azure-resource-manager/management/overview.md), в которой создается приложение-функция. Укажите значение `myResourceGroup`, которое используется в примерах, приведенных в этом кратком руководстве. Имя группы ресурсов должно быть уникальным в пределах подписки Azure.|

Введите `Y` или нажмите клавишу ВВОД для подтверждения.

Maven создаст файлы проекта в новой папке с именем _artifactId_, то есть `fabrikam-functions` в нашем примере. Выполните следующую команду, чтобы перейти в созданную папку проекта.
```bash
cd fabrikam-function
```

::: zone-end 
::: zone pivot="java-build-tools-gradle"
Используйте следующую команду, чтобы клонировать пример проекта:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Откройте `build.gradle` и измените `appName` в следующем разделе на уникальное имя, чтобы избежать конфликта доменных имен при развертывании в Azure. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```
::: zone-end

Откройте в текстовом редакторе новый файл Function.java из папки *src/main/java* и изучите созданный код. Этот код содержит [активируемую по HTTP-запросу](functions-bindings-http-webhook.md) функцию, которая возвращает текст запроса. 

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Локальное выполнение функции

Выполните следующую команду, чтобы собрать и запустить проект функции:

::: zone pivot="java-build-tools-maven" 
```bash
mvn clean package 
mvn azure-functions:run
```
::: zone-end 

::: zone pivot="java-build-tools-gradle"  
```bash
gradle jar --info
gradle azureFunctionsRun
```
::: zone-end 

При локальном выполнении проекта вы увидите следующие выходные данные Azure Functions Core Tools:

```output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
```

Вызовите функцию из командной строки, запустив cURL в новом окне терминала:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

```output
Hello AzureFunctions!
```
[Ключ функции](functions-bindings-http-webhook-trigger.md#authorization-keys) при локальном выполнении не требуется. Используйте `Ctrl+C` в терминале, чтобы остановить код функции.

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Развертывание функции для Azure

Приложение-функция и связанные ресурсы создаются в Azure при первом развертывании приложения-функции. Перед развертыванием выполните команду [az login](/cli/azure/authenticate-azure-cli) в Azure CLI, чтобы войти в подписку Azure. 

```azurecli
az login
```

> [!TIP]
> Если ваша учетная запись имеет доступ к нескольким подпискам, задайте подписку по умолчанию для текущего сеанса с помощью команды [az account set](/cli/azure/account#az-account-set). 

Используйте следующую команду, чтобы развернуть проект в виде нового приложения-функции. 


::: zone pivot="java-build-tools-maven" 
```bash
mvn azure-functions:deploy
```
::: zone-end 

::: zone pivot="java-build-tools-gradle"  
```bash
gradle azureFunctionsDeploy
```
::: zone-end

Это создаст в Azure такие ресурсы:

+ группа ресурсов; Ей присваивается имя, которое вы указали в параметре _resourceGroup_.
+ учетная запись хранения; Требуется для Функций Azure. Это имя создается случайным образом на основе требований к именованию учетных записей хранения.
+ План Службы приложений. Бессерверное размещение для приложения-функции в регионе, который указан в параметре _appRegion_. Это имя создается случайным образом.
+ Приложение-функция. Приложение-функция представляет собой минимальную единицу развертывания и выполнения для ваших функций. Ему присваивается имя из параметра _appName_, к которому добавляется случайное число. 

Развертывание также упаковывает файлы проекта и развертывает их в новом приложении-функции [из ZIP-файла](functions-deployment-technologies.md#zip-deploy) с включенным режимом выполнения из пакета.

После завершения развертывания вы увидите URL-адрес, который можно использовать для доступа к конечным точкам приложения-функции. Так как опубликованный нами триггер HTTP использует `authLevel = AuthorizationLevel.FUNCTION`, вам нужно получить ключ функции для вызова конечной точки функции по протоколу HTTP. Ключ функции проще всего получить на [Портал Azure].

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Получение URL-адреса триггера HTTP

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

Вы можете получить URL-адрес для активации функции (вместе с ключом функции) на портале Azure. 

1. Перейдите на [портал Azure], войдите в систему, введите _имя приложения-функции_ в поле **Поиск** в верхней части страницы и нажмите клавишу ВВОД.
 
1. В приложении-функции разверните **Функции (только для чтения)** , выберите созданную функцию и нажмите  **</> Получить URL-адрес функции** в правом верхнем углу. 

    ![Копирование URL-адреса функции с портала Azure](./media/functions-create-java-maven/get-function-url-portal.png)

1. Выберите вариант **по умолчанию (функциональная клавиша)** и нажмите **Копировать**. 

Теперь скопированный URL-адрес можно использовать для доступа к функции.

## <a name="verify-the-function-in-azure"></a>Проверка функции в Azure

Чтобы проверить работу приложения-функции в Azure с помощью `cURL`, замените URL из приведенного ниже примера реальным URL-адресом, который вы скопировали с портала.

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Это действие отправляет запрос POST в конечную точку функции с добавлением `AzureFunctions` в текст запроса. Вы увидите следующий ответ:

```output
Hello AzureFunctions!
```

> [!div class="nextstepaction"]
> [У меня есть проблема](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Дальнейшие действия

Итак, вы создали проект с функцией Java, активируемой по HTTP-запросу, запустили ее на своем локальном компьютере и развернули в Azure. Теперь расширьте свою функцию путем...

> [!div class="nextstepaction"]
> [Добавления выходной привязки очереди службы хранилища Azure](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Портал Azure]: https://portal.azure.com
