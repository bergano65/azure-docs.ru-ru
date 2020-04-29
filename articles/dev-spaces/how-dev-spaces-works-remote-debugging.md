---
title: Как выполняется удаленная отладка кода с помощью Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Описание процессов удаленной отладки в службе Kubernetes Azure с помощью Azure Dev Spaces
keywords: Azure Dev Spaces, пространства разработки, Docker, Kubernetes, Azure, AKS, служба Kubernetes Azure, контейнеры
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241404"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Как выполняется удаленная отладка кода с помощью Azure Dev Spaces

Azure Dev Spaces предоставляет несколько способов быстрого перебора и отладки Kubernetes приложений и совместной работы с командой в кластере службы Kubernetes Azure (AKS). После запуска проекта в пространстве разработки Azure Dev Spaces предоставляет способ подключения и отладки работающего приложения в AKS.

В этой статье описывается, как работает удаленная отладка с использованием пространств разработки.

## <a name="debug-your-code"></a>Отладка кода

Для приложений Java, .NET Core и Node. js можно выполнить отладку приложения, работающего непосредственно в пространстве разработки, с помощью Visual Studio Code или Visual Studio. Visual Studio Code и Visual Studio предоставляют средства для подключения к сфере разработки, запуска приложения и подключения отладчика. После выполнения `azds prep`проект можно открыть в Visual Studio Code или Visual Studio. Visual Studio Code или Visual Studio создаст собственные файлы конфигурации для подключения, которые отделены от выполнения `azds prep`. В Visual Studio Code или Visual Studio можно задать точки останова и запустить приложение в пространстве разработки.

![Отладка кода](media/get-started-node/debug-configuration-nodejs2.png)

При запуске приложения с помощью Visual Studio Code или Visual Studio для отладки они управляют запуском и подключением к пространству разработки таким же образом, как и `azds up`при запуске. Кроме того, средства на стороне клиента в Visual Studio Code и Visual Studio предоставляют дополнительный параметр с определенной информацией для отладки. Параметр содержит имя образа отладчика, расположение отладчика в образе отладчика и конечное расположение в контейнере приложения для подключения папки отладчика.

Изображение отладчика автоматически определяется средствами на стороне клиента. В нем используется метод, аналогичный использованному во время работы `azds prep`Dockerfile и Helm диаграмма. После подключения отладчика к образу приложения он запускается с помощью `azds exec`.

## <a name="next-steps"></a>Дальнейшие шаги

Чтобы приступить к использованию Azure Dev Spaces для удаленной отладки проекта, см. следующие краткие руководства.

* [Быстрое перебор и отладка с помощью Visual Studio Code и Java][quickstart-java]
* [Быстрое перебор и отладка с помощью Visual Studio Code и .NET][quickstart-netcore]
* [Быстрое перебор и отладка с помощью Visual Studio Code и Node. js][quickstart-node]
* [Быстрое перебор и отладка с помощью Visual Studio и .NET Core][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
