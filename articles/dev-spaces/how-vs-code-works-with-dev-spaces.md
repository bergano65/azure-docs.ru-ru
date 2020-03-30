---
title: Как визуальный код студии работает с пространствами Azure Dev
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Узнайте, как Visual Studio Code и Пространства Azure Dev помогут вам отладить и быстро итерировать приложения Kubernetes
keywords: Пространства Azure Dev, Dev Spaces, Докер, Кубернетес, Лазурный берег, AKS, служба Azure Kubernetes, контейнеры
ms.openlocfilehash: 91440e59fdb8c21579ef1f04e78e66f933221ba0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240448"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Как визуальный код студии работает с пространствами Azure Dev

Для подготовки, запуска и отладки служб с помощью Azure Dev Spaces можно использовать Visual Studio Code и [расширение Azure Dev Spaces.][azds-extension] С помощью Visual Studio Code и расширения Azure Dev Spaces вы можете:

* Создание активов для работы и отладки служб в AKS
* Запустите службы Java, Node.js и .NET Core в пространстве разработчиков
* Непосредственно отладить Java, Node.js и .NET Основные службы, работающие в пространстве разработчиков

## <a name="generate-assets"></a>Создание активов

Visual Studio Code и расширение Azure Dev Spaces создают следующие ресурсы для вашего проекта:

* Dockerfiles для Java-приложений с использованием приложений Maven, Node.js и .NET Core
* Диаграммы руля для почти любого языка с Dockerfile
* Файл, `azds.yaml` который является [файлом конфигурации Azure Dev Spaces][azds-yaml] для вашего проекта
* Папка `.vscode` с конфигурацией Visual Studio Code для Java-приложений с использованием приложений Maven, Node.js и .NET Core

Диаграмма Dockerfile, Helm `azds.yaml` и файлы — `azds prep`это те же активы, генерируемые при запуске. Эти файлы также могут быть использованы за пределами кода Visual `azds up`Studio для запуска проекта в AKS, например, для выполнения. Папка `.vscode` используется только visual Studio код для запуска проекта в AKS от Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Запустите свой сервис в AKS

После создания ресурсов для проекта можно запустить службы Java, Node.js и .NET Core в существующем пространстве разработчиков из Visual Studio Code. На странице *Debug* Visual Studio Code можно вызвать конфигурацию `.vscode` запуска из каталога для запуска проекта.

Необходимо создать кластер AKS и включить пространства Azure Dev в кластере за пределами Visual Studio Code. Например, для этой настройки можно использовать портал Azure CLI или портал Azure. Можно повторно использовать существующие диаграммы Dockerfiles, Helm и `azds.yaml` файлы, созданные за `azds prep`пределами Visual Studio Code, такие как активы, генерируемые запуском. Если вы повторно используете активы, генерируемые за `.vscode` пределами Visual Studio Code, вам все равно необходимо иметь каталог. Этот `.vscode` каталог может быть регенерирован с помощью кода Visual Studio и расширения Azure Dev Spaces и не будет перезаписывать существующие ресурсы.

Для проектов .NET Core необходимо установить [расширение C'NET][csharp-extension] для запуска службы .NET из Visual Studio Code. Также для Java-проектов, использующих Maven, необходимо установить [расширение Java Debugger для Azure Dev Spaces,][java-extension] а также [установить и настроить Maven][maven] для запуска Java-сервиса из Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Отожоблять свой сервис в AKS

После запуска проекта можно отладить службы Java, Node.js и .NET Core, работающие в пространстве разработчиков непосредственно из кода Visual Studio. Конфигурация запуска `.vscode` в каталоге обеспечивает дополнительную информацию об отладке для запуска службы с включенным отладкой в пространстве разработчиков. Visual Studio Code также прикрепляется к процессу отладки в запущенном контейнере в пространстве разработчиков, что позволяет устанавливать точки разрыва, проверять переменные и выполнять другие операции отладки.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Используйте визуальный код студии с пространствами Azure Dev

Вы можете увидеть Visual Studio Code и расширение Azure Dev Spaces, работая с Пространствами Azure Dev, в следующих быстрых запусках:

* [Быстро итерировать и отладить с Visual Studio code и Java][quickstart-java]
* [Быстро итерировать и отладить с визуальным кодом студии и .NET][quickstart-netcore]
* [Быстро итерировать и отладить с визуальным кодом студии и Node.js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
