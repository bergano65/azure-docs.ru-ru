---
title: Настройка Функций Azure для настраиваемых поставщиков Azure
description: В руководстве показано, как создать приложение-функцию Azure и настроить его для работы с настраиваемыми поставщиками Azure
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 6b5ab6948d382a9925c9ced91e04f360ecf51a0e
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173028"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Настройка Функций Azure для настраиваемых поставщиков Azure

Настраиваемым поставщиком называется контракт между Azure и конечной точкой. Настраиваемые поставщики позволяют изменять рабочие процессы в Azure. В этом руководстве демонстрируется процесс настройки приложения-функции Azure для работы в качестве конечной точки настраиваемого поставщика.

## <a name="create-the-azure-function-app"></a>Создание приложения-функции Azure

> [!NOTE]
> Выполнив инструкции из этого руководства, вы создадите простую конечную точку службы, которая использует приложение-функцию Azure. При этом настраиваемый поставщик может использовать любую общедоступную конечную точку. Это может быть Azure Logic Apps, Azure API Management или функция Web Apps в Службе приложений Azure.

Прежде чем начать работу с этим руководством, выполните инструкции из руководства [Создание первой функции Azure на портале Azure](../azure-functions/functions-create-first-azure-function.md), чтобы создать функцию веб-перехватчика .NET Core, которую можно изменить на портале Azure. Это является предварительным требованиям для начала работы.

## <a name="install-azure-table-storage-bindings"></a>Установка привязок к табличному хранилищу Azure

Чтобы установить привязки к табличному хранилищу Azure, выполните следующее:

1. Перейдите на вкладку **Интеграция** для HttpTrigger.
1. Щелкните **Новое входное значение**.
1. Щелкните **Табличное хранилище Azure**.
1. Установите расширение Microsoft.Azure.WebJobs.Extensions.Storage, если оно еще не установлено.
1. В поле **Имя параметра таблицы** введите **tableStorage**.
1. В поле **Имя таблицы** введите **myCustomResources**.
1. Щелкните **Сохранить**, чтобы сохранить обновленный входной параметр.

![Настраиваемый поставщик с привязками таблиц](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Обновление методов HTTP RESTful

Чтобы настроить в функции Azure методы запроса из настраиваемого поставщика RESTful, выполните следующее:

1. Перейдите на вкладку **Интеграция** для HttpTrigger.
1. В разделе **Выбранные методы HTTP** выберите **GET**, **POST**, **DELETE** и **PUT**.

![Настраиваемый поставщик с методами HTTP](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Добавление пакетов NuGet диспетчера ресурсов Azure

> [!NOTE]
> Если в каталоге проекта отсутствует файл проекта C#, его можно добавить вручную. Он также автоматически появится после установки расширения Microsoft.Azure.WebJobs.Extensions.Storage для приложения-функции.

Затем обновите файл проекта C#, подключив библиотеки NuGet. Эти библиотеки упрощают анализ входящих запросов от настраиваемых поставщиков. Выполните действия, описанные в статье о[добавлении расширений на портале](../azure-functions/install-update-binding-extensions-manual.md), и обновите файл проекта C#, добавив в него следующие ссылки на пакеты:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Следующий XML-элемент служит примером файла проекта C#:

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

При работе с этим руководством вы настроили приложение-функцию Azure в качестве конечной точки настраиваемого поставщика.

Сведения о том, как создать конечную точку RESTful для настраиваемого поставщика,см. в [этом руководстве](./tutorial-custom-providers-function-authoring.md).

