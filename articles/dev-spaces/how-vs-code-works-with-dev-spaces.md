---
title: Как Visual Studio Code работает с Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: Как Visual Studio Code работает с Azure Dev Spaces
keywords: Azure Dev Spaces, пространства разработки, Docker, Kubernetes, Azure, AKS, служба Kubernetes Azure, контейнеры
ms.openlocfilehash: 0d80643b366b6d7313f24e73258056e492eb56fc
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297868"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Как Visual Studio Code работает с Azure Dev Spaces

Вы можете использовать Visual Studio Code и [расширение Azure dev Spaces][azds-extension] для подготовки, запуска и отладки служб с помощью Azure dev Spaces. С помощью Visual Studio Code и расширения Azure Dev Spaces можно:

* Создание ресурсов для запуска и отладки служб в AKS
* Запуск служб Java, Node. js и .NET Core в пространстве разработки
* Непосредственная отладка служб Java, Node. js и .NET Core, работающих в пространстве разработки

## <a name="generate-assets"></a>Создание ресурсов

Visual Studio Code и расширение Azure Dev Spaces создают следующие ресурсы для проекта:

* Файлы dockerfile для приложений Java с помощью Maven, приложений Node. js и приложений .NET Core
* Helm диаграммы для практически любого языка с помощью Dockerfile
* Файл, который является [файлом конфигурации Azure dev Spaces][azds-yaml] для проекта `azds.yaml`
* `.vscode` Папка с конфигурацией Visual Studio Code запуска проекта для приложений Java с помощью Maven, приложений Node. js и приложений .NET Core.

Dockerfile, Helm диаграмма и `azds.yaml` файлы — это те же ресурсы, которые создаются при запуске. `azds prep` Эти файлы можно также использовать вне Visual Studio Code для запуска проекта в AKS, например `azds up`для запуска. Эта `.vscode` папка используется в Visual Studio Code для запуска проекта в AKS из Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Запуск службы в AKS

После создания ресурсов для проекта можно запустить службы Java, Node. js и .NET Core в существующем пространстве разработки из Visual Studio Code. На странице *Отладка* Visual Studio Code можно вызвать конфигурацию запуска из `.vscode` каталога, чтобы запустить проект.

Необходимо создать кластер AKS и включить Azure Dev Spaces в кластере вне Visual Studio Code. Например, для этой установки можно использовать Azure CLI или портал Azure. Можно повторно использовать существующие диаграммы файлы dockerfile, Helm и `azds.yaml` файлы, созданные за пределами Visual Studio Code, например ресурсы, созданные при выполнении. `azds prep` Если вы выполняете повторное использование ресурсов, созданных за пределами Visual Studio Code, `.vscode` вам по-прежнему нужен каталог. Этот `.vscode` каталог можно повторно создать с помощью Visual Studio Code и расширения Azure dev Spaces и не будет перезаписывать существующие ресурсы.

Для проектов .NET Core необходимо установить [ C# расширение][csharp-extension] installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension] , а также [Maven установленные и настроенные][Maven] для запуска службы Java из Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Отладка службы в AKS

После запуска проекта можно выполнить отладку служб Java, Node. js и .NET Core, работающих в пространстве разработки, непосредственно из Visual Studio Code. Конфигурация запуска в `.vscode` каталоге предоставляет дополнительные отладочные сведения для запуска службы с включенной отладкой в пространстве разработки. Visual Studio Code также присоединяется к процессу отладки в работающем контейнере в пространствах разработки, что позволяет задавать точки останова, проверять переменные и выполнять другие операции отладки.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Использование Visual Studio Code с Azure Dev Spaces

Visual Studio Code и расширение Azure Dev Spaces, работающее с Azure Dev Spaces, можно просмотреть в следующих кратких руководствах:

* [Разработка с использованием Java][quickstart-java]
* [Разработка с использованием .NET][quickstart-netcore]
* [Разработка с помощью Node. js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
