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
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325722"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Используйте пользовательский канал NuGet с Azure Dev Spaces

Веб-канал NuGet обеспечивает удобный способ добавления источников пакетов в проект. Пространства Azure Dev должны получить доступ к этому каналу для того, чтобы зависимые были должным образом установлены в контейнере Docker.

## <a name="set-up-a-nuget-feed"></a>Настройка веб-канала NuGet

Добавьте [ссылку](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) на пакет `*.csproj` зависимости `PackageReference` в файл под узлом. Пример:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Создайте файл [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) в папке `packageSources` `packageSourceCredentials` проекта и установите и разделы для вашего канала NuGet. Раздел `packageSources` содержит URL-адрес канала, который должен быть доступен из кластера AKS. Это `packageSourceCredentials` учетные данные для доступа к каналу. Пример:

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

Обновление Dockerfiles для `NuGet.Config` копирования файла на изображение. Пример:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> На `NuGet.Config`Windows, `Nuget.Config`, `nuget.config` и все работает как действительные имена файлов. На Linux, `NuGet.Config` только это действительное имя файла для этого файла. Поскольку Azure Dev Spaces использует Docker и `NuGet.Config`Linux, этот файл должен быть назван . Вы можете исправить именование `dotnet restore --configfile nuget.config`вручную или при запуске.


Если вы используете Git, вы не должны иметь учетные данные для вашего nuGet канал в управлении версиями. Добавьте `NuGet.Config` `.gitignore` к проекту, чтобы `NuGet.Config` файл не был добавлен в управление версией. Пространства Azure Dev будут нужны в процессе создания изображения контейнера, но по `.gitignore` `.dockerignore` умолчанию он уважает правила, определенные в синхронизации и во время синхронизации. Чтобы изменить значение по умолчанию и разрешить Azure Dev Spaces синхронизировать `NuGet.Config` файл, обновите `azds.yaml` файл:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Если вы не используете Git, вы можете пропустить этот шаг.

При следующем запуске `azds up` `F5` или запуске в Visual Studio Code или Visual Studio `NuGet.Config` Azure Dev Spaces синхронизирует файл, используйте его для установки зависимостей пакетов.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [NuGet и как он работает.](https://docs.microsoft.com/nuget/what-is-nuget)