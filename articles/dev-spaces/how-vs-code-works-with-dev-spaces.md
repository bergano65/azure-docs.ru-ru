---
title: Как Visual Studio Code работает с Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Узнайте, как Visual Studio Code и Azure Dev Spaces помочь в отладке и быстром переборе Kubernetes приложений.
keywords: Azure Dev Spaces, пространства разработки, Docker, Kubernetes, Azure, AKS, служба Kubernetes Azure, контейнеры
ms.openlocfilehash: 91440e59fdb8c21579ef1f04e78e66f933221ba0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240448"
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
* `azds.yaml` Файл, который является [файлом конфигурации Azure dev Spaces][azds-yaml] для проекта
* `.vscode` Папка с конфигурацией Visual Studio Code запуска проекта для приложений Java с помощью Maven, приложений Node. js и приложений .NET Core.

Dockerfile, Helm диаграмма и `azds.yaml` файлы — это те же ресурсы, которые создаются при `azds prep`запуске. Эти файлы можно также использовать вне Visual Studio Code для запуска проекта в AKS, например для запуска `azds up`. Эта `.vscode` папка используется в Visual Studio Code для запуска проекта в AKS из Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Запуск службы в AKS

После создания ресурсов для проекта можно запустить службы Java, Node. js и .NET Core в существующем пространстве разработки из Visual Studio Code. На странице *отладка* Visual Studio Code можно вызвать конфигурацию запуска из `.vscode` каталога, чтобы запустить проект.

Необходимо создать кластер AKS и включить Azure Dev Spaces в кластере вне Visual Studio Code. Например, для этой установки можно использовать Azure CLI или портал Azure. Можно повторно использовать существующие диаграммы файлы dockerfile, Helm и `azds.yaml` файлы, созданные за пределами Visual Studio Code, например ресурсы, созданные при `azds prep`выполнении. Если вы выполняете повторное использование ресурсов, созданных за пределами Visual Studio Code, `.vscode` вам по-прежнему нужен каталог. Этот `.vscode` каталог можно повторно создать с помощью Visual Studio Code и расширения Azure dev Spaces и не будет перезаписывать существующие ресурсы.

Для проектов .NET Core необходимо установить [расширение C#][csharp-extension] , чтобы запустить службу .net из Visual Studio Code. Кроме того, для проектов Java, использующих Maven, необходимо установить [отладчик Java для Azure dev Spacesного расширения][java-extension] , а также [Maven, установленный и настроенный][maven] для запуска службы Java из Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Отладка службы в AKS

После запуска проекта можно выполнить отладку служб Java, Node. js и .NET Core, работающих в пространстве разработки, непосредственно из Visual Studio Code. Конфигурация запуска в `.vscode` каталоге предоставляет дополнительные отладочные сведения для запуска службы с включенной отладкой в пространстве разработки. Visual Studio Code также присоединяется к процессу отладки в работающем контейнере в пространствах разработки, что позволяет задавать точки останова, проверять переменные и выполнять другие операции отладки.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Использование Visual Studio Code с Azure Dev Spaces

Visual Studio Code и расширение Azure Dev Spaces, работающее с Azure Dev Spaces, можно просмотреть в следующих кратких руководствах:

* [Быстрое перебор и отладка с помощью Visual Studio Code и Java][quickstart-java]
* [Быстрое перебор и отладка с помощью Visual Studio Code и .NET][quickstart-netcore]
* [Быстрое перебор и отладка с помощью Visual Studio Code и Node. js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
