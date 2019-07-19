---
title: Как использовать пользовательский веб-канал NuGet в Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Использование настраиваемого веб-канала NuGet для предоставления доступа и использования пакетов NuGet в Azure Dev Spaces.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
manager: gwallace
ms.openlocfilehash: 44a87491d276e09e1fa8fed3f5e6803648c3e4a2
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305399"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Использование настраиваемого веб-канала NuGet в Azure Dev Spaces

Веб-канал NuGet обеспечивает удобный способ добавления источников пакетов в проект. Azure Dev Spaces необходимо получить доступ к этому веб-каналу, чтобы обеспечить правильную установку зависимостей в контейнере DOCKER.

## <a name="set-up-a-nuget-feed"></a>Настройка веб-канала NuGet

Добавьте [ссылку на пакет](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) для зависимости в `*.csproj` файл `PackageReference` в узле. Пример:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Создайте файл [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) в папке проекта и задайте `packageSources` разделы и `packageSourceCredentials` для своего веб-канала NuGet. `packageSources` Раздел содержит URL-адрес канала, который должен быть общедоступным. `packageSourceCredentials` — Это учетные данные для доступа к веб-каналу. Пример:

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Обновите файлы dockerfile, чтобы скопировать `NuGet.Config` файл в образ. Пример:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> В Windows, `NuGet.Config` `Nuget.Config`, и `nuget.config` все работают как допустимые имена файлов. В Linux для этого `NuGet.Config` файла допустимо только имя файла. Поскольку Azure Dev Spaces использует DOCKER и Linux, этот файл должен иметь имя `NuGet.Config`. Имя можно исправить вручную или с помощью команды `dotnet restore --configfile nuget.config`.


Если вы используете Git, у вас не должно быть учетных данных для веб-канала NuGet в системе управления версиями. Добавьте `NuGet.Config` `NuGet.Config` в проект для проекта, чтобы файл не добавлялся в систему управления версиями. `.gitignore` Azure dev Spaces потребуется этот файл в процессе сборки образа контейнера, но по умолчанию он учитывает правила, определенные в `.gitignore` и `.dockerignore` во время синхронизации. Чтобы изменить значение по умолчанию и разрешить Azure dev Spaces синхронизировать `NuGet.Config` файл, `azds.yaml` обновите файл:

```yaml
build:
useGitIgnore: true
ignore:
- “!NuGet.Config”
```

Если вы не используете Git, этот шаг можно пропустить.

При следующем выполнении `azds up` или попадании `F5` в Visual Studio Code или Visual Studio Azure dev Spaces синхронизирует `NuGet.Config` файл, используйте его для установки зависимостей пакетов.

## <a name="next-steps"></a>Следующие шаги

Узнайте больше о [NuGet и принципах его работы](https://docs.microsoft.com/nuget/what-is-nuget).