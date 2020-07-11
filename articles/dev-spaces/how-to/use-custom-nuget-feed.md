---
title: Использование пользовательского веб-канала NuGet
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Использование настраиваемого веб-канала NuGet для предоставления доступа и использования пакетов NuGet в Azure Dev Spaces.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
manager: gwallace
ms.openlocfilehash: 77c7b733b12d9b352f9a806cadc0f900b9283ef3
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229283"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Использование пользовательского веб-канала NuGet с Azure Dev Spaces

Веб-канал NuGet обеспечивает удобный способ добавления источников пакетов в проект. Azure Dev Spaces необходимо получить доступ к этому веб-каналу, чтобы обеспечить правильную установку зависимостей в контейнере DOCKER.

## <a name="set-up-a-nuget-feed"></a>Настройка веб-канала NuGet

Добавьте [ссылку на пакет](/nuget/consume-packages/package-references-in-project-files) для зависимости в файл в `*.csproj` `PackageReference` узле. Вот несколько примеров:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Создайте файл [NuGet.Config](/nuget/reference/nuget-config-file) в папке проекта и задайте `packageSources` `packageSourceCredentials` разделы и для своего веб-канала NuGet. `packageSources`Раздел содержит URL-адрес канала, который должен быть доступен из кластера AKS. `packageSourceCredentials`— Это учетные данные для доступа к веб-каналу. Вот несколько примеров:

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

Обновите файлы dockerfile, чтобы скопировать `NuGet.Config` файл в образ. Вот несколько примеров:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> В Windows, `NuGet.Config` , `Nuget.Config` и `nuget.config` все работают как допустимые имена файлов. В Linux `NuGet.Config` для этого файла допустимо только имя файла. Поскольку Azure Dev Spaces использует DOCKER и Linux, этот файл должен иметь имя `NuGet.Config` . Имя можно исправить вручную или с помощью команды `dotnet restore --configfile nuget.config` .


Если вы используете Git, у вас не должно быть учетных данных для веб-канала NuGet в системе управления версиями. Добавьте `NuGet.Config` в `.gitignore` проект для проекта, чтобы `NuGet.Config` файл не добавлялся в систему управления версиями. Azure Dev Spaces потребуется этот файл в процессе сборки образа контейнера, но по умолчанию он учитывает правила, определенные в `.gitignore` и `.dockerignore` во время синхронизации. Чтобы изменить значение по умолчанию и разрешить Azure Dev Spaces синхронизировать `NuGet.Config` файл, обновите `azds.yaml` файл:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Если вы не используете Git, этот шаг можно пропустить.

При следующем выполнении `azds up` или попадании `F5` в Visual Studio Code или Visual Studio Azure dev Spaces синхронизирует `NuGet.Config` файл, используйте его для установки зависимостей пакетов.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [NuGet и принципах его работы](/nuget/what-is-nuget).
