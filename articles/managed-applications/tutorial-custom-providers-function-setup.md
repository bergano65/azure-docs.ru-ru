---
title: Настройка Функций Azure для настраиваемых поставщиков Azure
description: В этом руководстве рассказывается, как создать функцию Azure и настроить ее для работы с настраиваемыми поставщиками Azure
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7e4de43659db88bfd9aad40cc3b9f1753189bba
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799123"
---
# <a name="setup-azure-functions-for-azure-custom-providers"></a>Настройка Функций Azure для настраиваемых поставщиков Azure

Настраиваемые поставщики позволяют настраивать рабочие процессы в Azure. Настраиваемый поставщик представляет собой контракт между Azure и `endpoint`. В этом руководстве описан процесс настройки функции Azure для работы в качестве настраиваемого поставщика`endpoint`.

Это руководство разделено на следующие разделы:

- Создание функции Azure
- Настройка привязки таблицы Azure
- Обновление методов HTTP RESTful
- Добавление пакетов NuGet диспетчера ресурсов Azure

Это руководство основано на следующих руководствах:

- [Создание первой функции Azure на портале Azure](../azure-functions/functions-create-first-azure-function.md)

## <a name="creating-the-azure-function"></a>Создание функции Azure

> [!NOTE]
> В этом руководстве мы создадим простую конечную точку службы, используя функцию Azure, но настраиваемый поставщик может использовать любую общедоступную `endpoint`. Azure Logic Apps, Azure API Management и Azure Web Apps — это отличные альтернативы.

Чтобы начать работу с этим руководством, необходимо следовать руководству [Создание первой функции Azure на портале Azure](../azure-functions/functions-create-first-azure-function.md). В этом руководстве будет создана функция веб-перехватчика .Net Core, которую можно изменить на портале Azure.

## <a name="install-azure-table-bindings"></a>Настройка привязки таблицы Azure

В этом разделе приведены быстрые действия по настройке привязки хранилища таблиц Azure.

1. Перейдите на вкладку `Integrate` для HttpTrigger.
2. Щелкните `+ New Input`.
3. Выберите `Azure Table Storage`.
4. Установите `Microsoft.Azure.WebJobs.Extensions.Storage`, если они еще не установлены.
5. Обновите `Table parameter name` на "tableStorage" и `Table name` на "myCustomResources".
6. Сохраните обновленный входной параметр.

![Обзор пользовательского поставщика](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Обновление методов HTTP RESTful

В этом разделе будут описаны быстрые действия по настройке функции Azure для включения методов запроса RESTful настраиваемого поставщика.

1. Перейдите на вкладку `Integrate` для HttpTrigger.
2. Обновите `Selected HTTP methods` к: GET, POST, DELETE и PUT.

![Обзор пользовательского поставщика](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="modifying-the-csproj"></a>Изменение пcsproj

> [!NOTE]
> Если csproj отсутствует в каталоге, его можно добавить вручную, или он появится после того, как в функции будет установлено расширение `Microsoft.Azure.WebJobs.Extensions.Storage`.

Затем мы обновим файл csproj, чтобы добавить в него полезные библиотеки NuGet, которые упростят анализ входящих запросов от настраиваемых поставщиков. Для этого выполните следующие действия, описанные в разделе [Установка или обновление привязки расширений функций Azure вручную с портала](../azure-functions/install-update-binding-extensions-manual.md), и обновите csproj, добавив в него следующие ссылки на пакеты:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Пример CSPROJ-файла:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>Дополнительная информация

В этой статье мы настроим функцию Azure для работы в качестве настраиваемого поставщика Azure`endpoint`. Чтобы узнать, как создать собственного поставщика RESTful`endpoint`, перейдите к следующей статье.

- [Руководство. Создание конечной точки для пользовательского поставщика RESTful](./tutorial-custom-providers-function-authoring.md)
