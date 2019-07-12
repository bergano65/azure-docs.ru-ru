---
title: Как работает Visual Studio Code с пробелами разработки Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: Как работает Visual Studio Code с пробелами разработки Azure
keywords: Azure пробелы разработки, разработки пробелы, Docker, Kubernetes, Azure, AKS, служба Azure Kubernetes, контейнеры
ms.openlocfilehash: a7ec20908b75ae07532c16daab8950ace9cd67ae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712154"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Как работает Visual Studio Code с пробелами разработки Azure

Можно использовать Visual Studio Code и [расширение Azure Dev пробелы][azds-extension] для подготовки, выполнения и отладки служб Azure Dev пробелов. С помощью Visual Studio Code и расширения пробелы разработки Azure вы можете:

* Создать ресурсы для запуска и отладки служб в AKS
* Запускать службы на Java, Node.js и .NET Core в пространстве разработки
* Прямой отладки в пространстве разработки служб Java, Node.js и .NET Core

## <a name="generate-assets"></a>Создать ресурсы

Visual Studio Code и расширения пробелы разработки Azure создайте следующие ресурсы для вашего проекта:

* Файлы Dockerfile для приложений Java с помощью Maven, приложений Node.js и приложений .NET Core
* Чарты helm для практически на любом языке с помощью Dockerfile
* `azds.yaml` Файл, который является [файл конфигурации Azure Dev пробелы][azds-yaml] для проекта
* Объект `.vscode` папки с конфигурацией запуска Visual Studio Code проекта для приложений Java с помощью Maven, приложений Node.js и приложений .NET Core

Файл Dockerfile, диаграмме Helm и `azds.yaml` файлы находятся эти ресурсы, созданные при выполнении `azds prep`. Эти файлы можно также использовать вне Visual Studio code для запуска проекта в AKS, например запуск `azds up`. `.vscode` Папки используется только с Visual Studio code для запуска проекта в AKS из Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Запустить службу в AKS

После создания ресурсов для вашего проекта, позволяет выполнять ваши службы Java, Node.js и .NET Core в существующее пространство разработки из кода Visual Studio. В *Отладка* страницы из Visual Studio Code, вы можете вызвать конфигурацию запуска из `.vscode` directory, чтобы запустить проект.

Необходимо создать кластер AKS и включить пробелы разработки Azure в кластере за пределами Visual Studio Code. Например можно использовать Azure CLI или портала Azure для выполнения этой установки. Вы можете повторно использовать существующие файлы Dockerfile, чарты Helm и `azds.yaml` файлы, созданные за пределами Visual Studio Code, такие как ресурсы при выполнении `azds prep`. При повторном использовании ресурсов, созданных за пределами Visual Studio Code, необходимо по-прежнему иметь `.vscode` каталога. Это `.vscode` каталог можно создать повторно с Visual Studio code и расширения Azure Dev пробелы и не перезаписывает существующие ресурсы.

Для проектов .NET Core, необходимо иметь [ C# расширение][csharp-extension] installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension] установленных, а также [Maven установлен и настроен][maven] для выполнения Java Служба Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Отладка службы в AKS

После запуска проекта, можно отлаживать работу Java, Node.js и .NET Core служб в пространстве разработки непосредственно из Visual Studio Code. Конфигурация запуска в `.vscode` directory предоставляет дополнительных сведений об отладке для запуска службы с отладкой в области разработки. Visual Studio Code также присоединяет процесс отладки в выполняемом контейнере в вашей разработки пробелы, что позволяет задавать точки останова, проверять значения переменных и выполнять другие операции отладки.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Используйте Visual Studio Code с пробелами разработки Azure

Вы увидите в Visual Studio Code и расширения пробелы разработки Azure, работа с пространствами разработки Azure в следующих кратких руководств:

* [Разработка с помощью Java][quickstart-java]
* [Разработка с помощью .NET][quickstart-netcore]
* [Разработка с помощью Node.js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md