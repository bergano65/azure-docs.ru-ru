---
title: Руководство. Обновление приложения Сетки Azure Service Fabric | Документы Майкрософт
description: Узнайте, как обновить приложение Service Fabric с помощью Visual Studio
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 23809abd06d626eb87e5d5d15d265f1769b97b66
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806745"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Руководство. Узнайте, как обновить приложение Service Fabric с помощью Visual Studio

Это руководство является четвертой частью цикла, в котором показано, как обновить приложение Сетки Azure Service Fabric непосредственно из Visual Studio. Обновления затронут как код, так и конфигурацию. Вы увидите, что действия по обновлению и публикация из среды Visual Studio совпадают.

Из этого руководства вы узнаете, как выполнить следующие задачи:
> [!div class="checklist"]
> * Обновление службы сетки Service Fabric с помощью Visual Studio

Из этого цикла руководств вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * [Создание приложения Сетки Service Fabric в Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Отладка приложения Сетки Service Fabric, выполняющегося в локальном кластере разработки](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Развертывание приложения Сетки Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Обновление приложения Сетки Service Fabric
> * [Удаление ресурсов Сетки Service Fabric](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Технические условия

Перед началом работы с этим руководством выполните следующие действия:

* Если вы еще не развернули приложение списка дел, следуйте инструкциям в статье [Руководство. Развертывание приложения Сетки Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Обновление службы сетки Service Fabric с помощью Visual Studio

В этой статье показано, как обновить микрослужбы в приложении. В этом примере мы изменим `WebFrontEnd` службы, чтобы отобразить категорию задач и увеличить объем ресурсов ЦП, ему присваивается. Затем мы выполним обновление развернутой службы.

## <a name="modify-the-config"></a>Изменение конфигурации

При создании приложения Service Fabric Mesh Visual studio добавляет **parameters.yaml** файл для каждой среды развертывания (облачных и локальных). В этих файлах можно определить параметры и их значения, которые затем можно ссылаться на файлы *.yaml сетки, например service.yaml или network.yaml.  Visual Studio предоставляет некоторые переменные, такие как ЦП, которую сможет использовать служба.

Мы обновим `WebFrontEnd_cpu` параметр для обновления ресурсов ЦП для `1.5` в ближайшем будущем, **WebFrontEnd** служба будет использоваться более интенсивно.

1. В **todolistapp** проекта, в разделе **сред** > **Cloud**откройте **parameters.yaml** файла. Изменить `WebFrontEnd_cpu`, равным `1.5`. Имя параметра, используемое имя службы `WebFrontEnd_` рекомендуется, чтобы отличить его от параметров с тем же именем, которые применяются к различным службам.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Откройте **WebFrontEnd** проекта **service.yaml** файл **WebFrontEnd** > **ресурсы службы**.

    Обратите внимание, что в `resources:` разделе `cpu:` присваивается `"[parameters('WebFrontEnd_cpu')]"`. Если проект строится для облака, значение `'WebFrontEnd_cpu` будет взято из **сред** > **Cloud** > **parameters.yaml** файл и будет `1.5`. Если проект строится для локального запуска, значение будет взято из **сред** > **локального** > **parameters.yaml** файл, и будет «0,5".

> [!Tip]
> По умолчанию файл параметров, который является одноранговым в отношении profile.yaml файл будет использоваться для предоставления значений для этого файла profile.yaml.
> К примеру, средах > облако > parameters.yaml предоставляет значения параметров для сред > Cloud > profile.yaml.
>
> Это можно переопределить, добавив следующий файл profile.yaml:`parametersFilePath=”relative or full path to the parameters file”` Например,  или `parametersFilePath=”..\CommonParameters.yaml”`.

## <a name="modify-the-model"></a>Изменение модели

Чтобы внести изменения кода, добавьте свойство `Category` в класс `ToDoItem` в файле `ToDoItem.cs`.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Затем обновите метод `Load()` в том же файле, чтобы присвоить категорию строке по умолчанию:

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>Изменение службы

Проект `WebFrontEnd` — это приложение ASP.NET Core с веб-страницей, которая отображает список дел. В проекте `WebFrontEnd` откройте `Index.cshtml` и добавьте две строки, указанные ниже, чтобы отобразить категорию задачи:

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Выполните сборку и запуск приложения, чтобы убедиться, что новый столбец категории отображается на веб-странице, в которой перечислены задачи.

## <a name="upgrade-the-app-from-visual-studio"></a>Обновление приложения из Visual Studio

Следует ли вы вносите, обновления кода или обновить конфигурацию (в данном случае мы делаем оба), обновите приложение Service Fabric Mesh в Azure, щелкнув правой кнопкой мыши **todolistapp** в Visual Studio, а затем выберите **публикации...**

Дальше появится диалоговое окно **Публикация приложения Service Fabric**.

Используйте **целевой профиль** раскрывающийся список для выбора файла profile.yaml необходимо использовать для этого развертывания. Мы обновляем приложения в облаке, поэтому мы выбираем **cloud.yaml** в раскрывающемся списке, который будет использовать `WebFrontEnd_cpu` значение 1,0, определенная в этом файле.

![Диалоговое окно публикации службы "Сетка Service Fabric" в Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Выберите учетную запись Azure и подписку. Задайте **расположение** в расположение, которое использовалось при первоначальной публикации приложение списка дел в Azure. В этой статье используется **восточная часть США**.

Задайте **группы ресурсов** к группе ресурсов, которые использовались при первоначальной публикации приложение списка дел в Azure.

Задайте **реестр контейнеров Azure** имя реестра контейнеров azure, который был создан при первоначальной публикации приложение списка дел в Azure.

В диалоговом окне публикации нажмите кнопку **Опубликовать**, чтобы обновить приложение списка дел в Azure.

Отслеживать ход выполнения обновления, выбрав **средства Service Fabric** области в Visual Studio **вывода** окна. 

После изображения, который отправляется в реестр контейнеров Azure, **состояние** ссылка отображается в выходных данных, которую можно щелкнуть для мониторинга развертывания на портале Azure.

После завершения обновления выходные данные в области **Средства Service Fabric** будут отображать IP-адрес и порт приложения в виде URL-адреса.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Откройте веб-браузер и перейдите к URL-адресу, чтобы увидеть работу сайта в Azure. Теперь вы видите веб-страницу, содержащую столбец категории.

## <a name="next-steps"></a>Дальнейшие действия

В этой части руководства было показано следующее.
> [!div class="checklist"]
> * Как обновить приложение Сетки Service Fabric с помощью Visual Studio

Перейдите к следующему руководству:
> [!div class="nextstepaction"]
> [Удаление ресурсов Сетки Service Fabric](service-fabric-mesh-tutorial-cleanup-resources.md)