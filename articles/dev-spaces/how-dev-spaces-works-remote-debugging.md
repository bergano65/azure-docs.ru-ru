---
title: Как работает удаленная отладка кода с помощью Пространства Azure Dev
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Описывает процессы удаленной отладки в службе Azure Kubernetes с azure Dev Spaces
keywords: Пространства Azure Dev, Dev Spaces, Докер, Кубернетес, Лазурный берег, AKS, служба Azure Kubernetes, контейнеры
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241404"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Как работает удаленная отладка кода с помощью Пространства Azure Dev

Azure Dev Spaces предоставляет вам множество способов быстрой итерации и отладки приложений Kubernetes и совместной работы с вашей командой в кластере Azure Kubernetes Service (AKS). После запуска проекта в пространстве разработчиков Azure Dev Spaces предоставляет возможность прикрепить и отладить запущенное приложение в AKS.

В этой статье описывается, как работает удаленная отладка с Dev Spaces.

## <a name="debug-your-code"></a>Отладка кода

Для приложений Java, .NET Core и Node.js можно отладить приложение, работая непосредственно в вашем пространстве разработчиков, используя Visual Studio Code или Visual Studio. Visual Studio Code и Visual Studio предоставляют инструменты для подключения к вашему пространству разработчиков, запуска приложения и присоединения отладчика. После `azds prep`запуска вы можете открыть свой проект в Visual Studio Code или Visual Studio. Visual Studio Code или Visual Studio будут создавать свои собственные `azds prep`файлы конфигурации для подключения, которое отделено от запущенного. Из Visual Studio Code или Visual Studio вы можете установить точки разрыва и запустить приложение в пространство разработчиков.

![Отладка кода](media/get-started-node/debug-configuration-nodejs2.png)

При запуске приложения с помощью Visual Studio Code или Visual Studio для отладки они `azds up`обрабатывают запуск и подключение к вашему пространству разработчиков так же, как и запуск. Кроме того, клиент-инструмент в Visual Studio Code и Visual Studio каждый предоставить дополнительный параметр с конкретной информацией для отладки. Параметр содержит имя изображения отладчика, расположение отладчика внутри изображения отладчика и место назначения в контейнере приложения для установки папки отладчика.

Изображение отладчика автоматически определяется инструментарием на стороне клиента. Он использует метод, аналогичный тому, который используется во `azds prep`время создания диаграммы Dockerfile и Helm при запуске. После того, как отладчик установлен в изображении `azds exec`приложения, он работает с помощью .

## <a name="next-steps"></a>Дальнейшие действия

Для начала использования Azure Dev Spaces для удаленной отладки проекта смотрите следующие быстрые запуски:

* [Быстро итерировать и отладить с Visual Studio code и Java][quickstart-java]
* [Быстро итерировать и отладить с визуальным кодом студии и .NET][quickstart-netcore]
* [Быстро итерировать и отладить с визуальным кодом студии и Node.js][quickstart-node]
* [Быстро итерировать и отладить с Visual Studio и .NET Core][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
