---
title: Как работает подготовка проекта для Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Описывает, как работает подготовка проекта с Azure Dev Spaces
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, контейнеры
ms.openlocfilehash: 24a54fffdc8e94493d2a4a9aeb1c5f02dcd192b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241638"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Как работает подготовка проекта для Azure Dev Spaces

Azure Dev Spaces предоставляет вам множество способов быстрой итерации и отладки приложений Kubernetes и совместной работы с вашей командой в кластере Azure Kubernetes Service (AKS). Dev Spaces может создавать диаграммы Dockerfiles и Helm для вашего проекта. Dev Spaces также создает и использует файл конфигурации для развертывания, запуска и отладки приложений Kubernetes в AKS. Все эти файлы находятся с кодом приложения и могут быть добавлены в систему управления версиями.

В этой статье описывается, что происходит, вы готовите свой проект для запуска в AKS с Dev Spaces.

## <a name="prepare-your-code"></a>Подготовьте код

Для того, чтобы запустить приложение в пространстве разработчиков, оно должно быть контейнеризировано, и вам нужно определить, как оно должно быть развернуто в Kubernetes. Для контейнеризации приложения вам нужен Dockerfile. Чтобы определить, как ваше приложение развертывается в Kubernetes, необходимо [создать диаграмму Helm.](https://docs.helm.sh/) Чтобы помочь в создании диаграммы Dockerfile и Helm для вашего `prep` приложения, инструменты со стороны клиента предоставляют команду:

```cmd
azds prep --enable-ingress
```

Команда `prep` будет смотреть на файлы в проекте и попытаться создать диаграмму Dockerfile и Helm для запуска приложения в Kubernetes. В настоящее `prep` время команда будет генерировать диаграмму Dockerfile и Helm со следующими языками:

* Java
* Node.js
* .NET Core

*Необходимо* запустить `prep` команду из каталога, содержащего исходный код. Запуск `prep` команды из правильного каталога позволяет инструментарию со стороны клиента идентифицировать язык и создать соответствующий Dockerfile для контейнеризации приложения. Вы также можете `prep` запустить команду из каталога, содержащего файл *pom.xml* для проектов Java.

Если вы `prep` выполняете команду из каталога, которая не содержит исходного кода, инструментирование на стороне клиента не будет генерировать Dockerfile. Он также будет отображать ошибку говорят: *Dockerfile не может быть создан из-за неподдерживаемых языка*. Эта ошибка также возникает, если инструментирование со стороны клиента не распознает тип проекта.

При запуске `prep` команды у вас есть возможность `--enable-ingress` указать флаг. Этот флаг говорит контроллеру создать доступную к Интернету конечную точку для этой службы. Если вы не указали этот флаг, служба доступна только из кластера или с помощью туннеля localhost, созданного инструментарием со стороны клиента. Вы можете включить или отключить это `prep` поведение после запуска команды, обновив сгенерированную диаграмму Helm.

Команда `prep` не заменит существующие диаграммы Dockerfiles или Helm, которые есть в вашем проекте. Если существующая диаграмма Dockerfile или Helm использует ту `prep` же конвенцию именования, что и файлы, генерируемые командой, `prep` команда пропустит генерацию этих файлов. В противном случае `prep` команда будет генерировать свой собственный график Dockerfile или Helm наряду с существующими файлами.

> [!IMPORTANT]
> Azure Dev Spaces использует диаграмму Dockerfile и Helm для создания и запуска кода, но вы можете изменить эти файлы, если хотите изменить способ построения и запуска проекта.

Команда `prep` также будет `azds.yaml` генерировать файл в корне проекта. Пространства Azure Dev используют этот файл для создания, установки, настройки и запуска приложения. В этом файле конфигурации перечислено местоположение диаграммы Dockerfile и Helm, а также предусмотрена дополнительная конфигурация поверх этих артефактов.

Вот пример файла azds.yaml, созданного с помощью [приложения .NET Core образца:](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

Файл, `azds.yaml` генерируемый командой, `prep` предназначен для работы для простого, единого сценария разработки проекта. Если ваш конкретный проект имеет повышенную сложность, может `prep` потребоваться обновить этот файл после запуска команды. Например, проект может потребовать некоторых изменений в процессе сборки или запуска в зависимости от потребностей в разработке или отладке. В проекте также может быть несколько приложений, которые требуют нескольких процессов сборки или другого содержимого сборки.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о запуске кода в пространстве разработчиков, см. [Как работает код с Azure Dev Spaces.][how-it-works-up]

Чтобы начать использовать Azure Dev Spaces для подготовки проекта для Azure Dev Space, смотрите следующие быстрые запуски:

* [Быстро итерировать и отладить с Visual Studio code и Java][quickstart-java]
* [Быстро итерировать и отладить с визуальным кодом студии и .NET][quickstart-netcore]
* [Быстро итерировать и отладить с визуальным кодом студии и Node.js][quickstart-node]
* [Быстро итерировать и отладить с Visual Studio и .NET Core][quickstart-vs]
* [Использование CLI для разработки приложения на Kubernetes][quickstart-cli]

[how-it-works-up]: how-dev-spaces-works-up.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md