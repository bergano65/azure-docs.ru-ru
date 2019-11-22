---
title: Как использовать пользовательский веб-канал NuGet в Azure Dev Spaces
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Использование настраиваемого веб-канала NuGet для предоставления доступа и использования пакетов NuGet в Azure Dev Spaces.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
manager: gwallace
ms.openlocfilehash: ee14d999872f6e739321c144831d60a4ae6f9388
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279952"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Использование настраиваемого веб-канала NuGet в Azure Dev Spaces

Веб-канал NuGet обеспечивает удобный способ добавления источников пакетов в проект. Azure Dev Spaces необходимо получить доступ к этому веб-каналу, чтобы обеспечить правильную установку зависимостей в контейнере DOCKER.

## <a name="set-up-a-nuget-feed"></a>Настройка веб-канала NuGet

Добавьте [ссылку на пакет](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) для зависимости в файл `*.csproj` в узле `PackageReference`. Например,

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Создайте файл [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) в папке проекта и задайте разделы `packageSources` и `packageSourceCredentials` для веб-канала NuGet. В разделе `packageSources` содержится URL-адрес канала, который должен быть доступен из кластера AKS. `packageSourceCredentials` являются учетными данными для доступа к веб-каналу. Например,

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

Обновите файлы dockerfile, чтобы скопировать файл `NuGet.Config` в образ. Например,

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> В Windows `NuGet.Config`, `Nuget.Config`и `nuget.config` все работают как допустимые имена файлов. В Linux для этого файла допустимо только `NuGet.Config` имя файла. Поскольку Azure Dev Spaces использует DOCKER и Linux, этот файл должен иметь имя `NuGet.Config`. Вы можете исправить имя вручную или запустив `dotnet restore --configfile nuget.config`.


Если вы используете Git, у вас не должно быть учетных данных для веб-канала NuGet в системе управления версиями. Добавьте `NuGet.Config` в `.gitignore` проекта, чтобы файл `NuGet.Config` не был добавлен в систему управления версиями. Azure Dev Spaces потребуется этот файл во время процесса сборки образа контейнера, но по умолчанию он учитывает правила, определенные в `.gitignore` и `.dockerignore` во время синхронизации. Чтобы изменить значение по умолчанию и разрешить Azure Dev Spaces синхронизировать `NuGet.Config` файл, обновите файл `azds.yaml`:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Если вы не используете Git, этот шаг можно пропустить.

При следующем запуске `azds up` или нажатии `F5` в Visual Studio Code или Visual Studio Azure Dev Spaces синхронизирует файл `NuGet.Config` использовать его для установки зависимостей пакетов.

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о [NuGet и принципах его работы](https://docs.microsoft.com/nuget/what-is-nuget).