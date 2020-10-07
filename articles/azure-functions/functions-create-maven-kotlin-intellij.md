---
title: Создание функции Azure с использованием Kotlin и IntelliJ
description: Сведения о создании и публикации простого бессерверного приложения, активируемого HTTP-запросом, в Azure с помощью Kotlin и IntelliJ.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: fa834e3ac4946c4f617e857342f850445eebfd30
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87055449"
---
# <a name="quickstart-create-your-first-http-triggered-function-with-kotlin-and-intellij"></a>Краткое руководство. Создание первой функции, активируемой HTTP-запросом, с помощью Kotlin и IntelliJ

В этой статье показано, как создать проект для [бессерверной](https://azure.microsoft.com/overview/serverless-computing/) функции с помощью IntelliJ IDEA и Apache Maven. Здесь также показано, как выполнить отладку кода функции локально в интегрированной среде разработки (IDE), а затем развернуть проект функции в Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

Для разработки функции с помощью Kotlin и IntelliJ необходимо установить следующее программное обеспечение:

- [Java Developer Kit (JDK) версии 8](https://aka.ms/azure-jdks)
- [Apache Maven 3.0](https://maven.apache.org) или более поздней версии
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), версия Community или Ultimate с Maven
- [Azure CLI](/cli/azure)
- [Azure Functions Core Tools](functions-run-local.md#v2) версии 2.x. Они предоставляют локальной среде разработки возможность записи, выполнения и отладки Функций Azure.

> [!IMPORTANT]
> Чтобы завершить выполнение шагов из этой статьи, переменной среде JAVA_HOME необходимо присвоить расположение установки JDK.

## <a name="create-a-functions-project"></a>Создание проекта Функций Azure

1. В IntelliJ IDEA выберите **Создать новый проект**.  
1. В левой панели окна **Новый проект** выберите **Maven**.
1. Установите флажок **Create from archetype** (Создать на основе архетипа), а затем выберите **Add Archetype** (Добавить архетип) для [azure-functions-kotlin-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. Заполните следующие поля в окне **Add Archetype** (Добавить архетип).
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-kotlin-archetype.
    - _Версия._ Используйте последнею версию, которую можно найти в [центральном репозитории](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
    ![Создание проекта Maven на основе архетипа в IntelliJ IDEA](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Нажмите кнопку **ОК**, а затем кнопку **Далее**.
1. Введите подробные сведения и выберите **Готово**.

Maven создает файлы проекта в новой папке с именем, которое соответствует значению _ArtifactId_. Созданный код проекта представляет собой простую функцию[активации HTTP](./functions-bindings-http-webhook.md), которая возвращает текст HTTP-запроса.

## <a name="run-functions-locally-in-the-ide"></a>Запуск функций в локальной среде IDE

> [!NOTE]
> Для локальных запуска и отладки функций, нужно установить [основные инструменты для Функций Azure версии 2](functions-run-local.md#v2).

1. Импортируйте изменения вручную или включите [автоматический импорт](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Откройте панель инструментов **Maven Projects** (Проекты Maven).
1. Разверните **Жизненный цикл**, а затем откройте **Пакет**. Решение будет создано и упаковано в созданном целевом каталоге.
1. Чтобы запустить локальную среду выполнения Azure, расширьте **Подключаемые модули** > **azure-functions** и откройте **azure-functions:run**.  
  ![Панель инструментов Maven для Функций Azure](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. После того как вы протестировали функцию, закройте диалоговое окно запуска. Только один узел функции может быть активным и работать локально одновременно.

## <a name="debug-the-function-in-intellij"></a>Отладка функции в IntelliJ

1. Чтобы запустить узел функции в режиме отладки, добавьте при выполнении функции **-DenableDebug** в качестве аргумента. Конфигурацию можно изменить либо в [целях Maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal),либо запустив в окне терминала следующую команду.  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   С помощью данной команды узел функции откроет порт отладки с номером 5005.

1. В меню **Run** (Запуск) выберите **Edit Configurations** (Изменить конфигурации).
1. Выберите **(+)** , чтобы добавить **Уд. доступ**.
1. Чтобы сохранить конфигурацию, заполните поля _Имя_ и _Параметры_, а затем нажмите кнопку **OK**.
1. После завершения установки для начала отладки выберите **Отладка < Имя конфигурации >** или нажмите комбинацию клавиш SHIFT+F9.

   ![Функции отладки в IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. По завершении остановите отладчик и запущенный процесс. Только один узел функции может быть активным и работать локально одновременно.

## <a name="deploy-the-function-to-azure"></a>Развертывание функции для Azure

1. Перед развертыванием функции в Azure необходимо [выполнить вход с помощью Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Разверните свой код в новой функции, используя целевой объект Maven `azure-functions:deploy`. Также в окне "Проекты Maven" можно выбрать параметр **azure-functions:deploy**.

   ```
   mvn azure-functions:deploy
   ```

1. После успешного развертывания функции, в выходных данных Azure CLI необходимо найти ее URL-адрес.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы развернули первую функцию Kotlin в Azure, ознакомьтесь с руководством разработчика по [функциям Java](functions-reference-java.md) для получения дополнительных сведений о разработке функций Java и Kotlin.
- Добавьте в проект дополнительные функции с помощью различных триггеров целевого объекта Maven`azure-functions:add`.
