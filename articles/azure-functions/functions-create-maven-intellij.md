---
title: Создание функции Azure с использованием Java и IntelliJ | Документация Майкрософт
description: Сведения о создание и публикации простого независимого HTTP-приложения в Azure с использованием функций Java и IntelliJ.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: azure functions, functions, event processing, compute, serverless architecture, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: da93c60b52edf509900adf89fb688a0596d9763b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877812"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Создание первой функции Azure с помощью Java и IntelliJ

Из этой статьи можно получить следующие сведения.
- Как создать функцию проекта [serverless](https://azure.microsoft.com/overview/serverless-computing/) с помощью IntelliJ IDEA и Apache Maven
- Шаги, которые необходимо выполнить для проверки и отладки функции в интегрированной среде разработки (IDE) на компьютере
- Инструкция для развертывания проекта функции в Функциях Azure

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

Чтобы разрабатывать функцию с помощью Java и IntelliJ, необходимо установить следующее программное обеспечение.

- [Java Developer Kit (JDK) версии 8](https://www.azul.com/downloads/zulu/)
- [Apache Maven 3.0](https://maven.apache.org) или более поздней версии
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), версия Community или Ultimate с Maven
- [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> Чтобы завершить выполнение шагов из этой статьи, переменной среде JAVA_HOME необходимо присвоить расположение установки JDK.

 Рекомендуется установить [Основные инструменты службы "Функции Azure" версии 2](functions-run-local.md#v2). Они предоставляют локальной среде разработки возможность записи, выполнения и отладки Функций Azure.

## <a name="create-a-functions-project"></a>Создание проекта Функций Azure

1. В IntelliJ IDEA выберите **Создать новый проект**.  
1. В левой панели окна **Новый проект** выберите **Maven**.
1. Установите флажок **Создать из архетипа**, а затем выберите **Добавить архетип** для [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
1. Заполните следующие поля в окне **Add Archetype** (Добавить архетип).
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-archetype
    - _Версия._ Используйте последнюю версию из [центральный репозиторий](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Создание проекта Maven из архетипа в IntelliJ IDEA](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Нажмите кнопку **ОК**, а затем щелкните **Создать**.
1. Введите подробные сведения и выберите **Готово**.

Maven создает файлы проекта в новой папке с именем, которое соответствует значению _ArtifactId_. Созданный код проекта представляет собой простую функцию[активации HTTP](/azure/azure-functions/functions-bindings-http-webhook), которая возвращает текст HTTP-запроса.

## <a name="run-functions-locally-in-the-ide"></a>Запуск функций в локальной среде IDE

> [!NOTE]
> Для локальных запуска и отладки функций, нужно установить [основные инструменты для Функций Azure версии 2](functions-run-local.md#v2).

1. Импортируйте изменения вручную или включите [автоматический импорт](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Откройте панель инструментов **Maven Projects** (Проекты Maven).
1. Разверните **Жизненный цикл**, а затем откройте **Пакет**. Решение будет создано и упаковано в созданном целевом каталоге.
1. Чтобы запустить локальную среду выполнения Azure, расширьте **Подключаемые модули** > **azure-functions** и откройте **azure-functions:run**.  
  ![Панель инструментов Maven для Функций Azure](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. После того как вы протестировали функцию, закройте диалоговое окно запуска. Только один узел функции может быть активным и работать локально одновременно.

## <a name="debug-the-function-in-intellij"></a>Отладка функции в IntelliJ

1. Чтобы запустить узел функции в режиме отладки, добавьте при выполнении функции **-DenableDebug** в качестве аргумента. Конфигурацию можно изменить либо в [целях Maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal),либо запустив в окне терминала следующую команду.  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   С помощью данной команды узел функции откроет порт отладки с номером 5005.

1. В меню **Run** (Запуск) выберите **Edit Configurations** (Изменить конфигурации).
1. Выберите **(+)**, чтобы добавить **Уд. доступ**.
1. Чтобы сохранить конфигурацию, заполните поля _Имя_ и _Параметры_, а затем нажмите кнопку **OK**.
1. После завершения установки для начала отладки выберите **Отладка < Имя конфигурации >** или нажмите комбинацию клавиш SHIFT+F9.

   ![Функции отладки в IntelliJ](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

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

- Дополнительные сведения о разработке функции Java см. в статье [Azure Functions Java developer guide](functions-reference-java.md) (Руководство разработчика Java для Функций Azure).
- Добавьте в проект дополнительные функции с помощью различных триггеров целевого объекта Maven`azure-functions:add`.
