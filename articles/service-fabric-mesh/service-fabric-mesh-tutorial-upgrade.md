---
title: Учебник - Обновление приложения Azure Службы Ткани Сетки
description: Это руководство является четвертой частью цикла, в котором показано, как обновить приложение Сетки Azure Service Fabric непосредственно из Visual Studio.
author: dkkapur
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 7cdb8868f760ef0f35ab90c06b411110f871738c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75351716"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Руководство. Узнайте, как обновить приложение Service Fabric с помощью Visual Studio

Это руководство является четвертой частью цикла, в котором показано, как обновить приложение Сетки Azure Service Fabric непосредственно из Visual Studio. Обновления затронут как код, так и конфигурацию. Вы увидите, что шаги для обновления и публикации из Visual Studio одинаковы.

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

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим руководством выполните следующие действия:

* Если вы еще не развернули приложение списка дел, следуйте инструкциям в статье [Руководство. Развертывание приложения Сетки Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Обновление службы сетки Service Fabric с помощью Visual Studio

В этой статье показано, как обновить микрослужбу в приложении. В этом примере мы `WebFrontEnd` изменим службу для отображения категории задач и увеличим количество предоставленного процессора. Затем мы выполним обновление развернутой службы.

## <a name="modify-the-config"></a>Изменение конфигурации

При создании приложения Service Fabric Mesh Visual Studio добавляет файл **parameters.yaml** для каждой среды развертывания (облачность и локальная). В этих файлах можно определить параметры и их значения, на которые можно ссылаться из файлов Mesh q.yaml, таких как service.yaml или network.yaml.  Visual Studio предоставляет некоторые переменные для вас, например, сколько процессора служба может использовать.

Мы обновим `WebFrontEnd_cpu` параметр для обновления `1.5` ресурсов cpu в ожидании, что служба **WebFrontEnd** будет более интенсивно использоваться.

1. В проекте **todolistapp,** под**облаком** **среды,** > откройте файл **parameters.yaml.** Изменение `WebFrontEnd_cpu`значения , `1.5`чтобы . Имя параметра предваряется `WebFrontEnd_` именем службы как наилучшей практикой, чтобы отличить его от параметров одного и того же имени, которые применяются к различным службам.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Откройте файл **webFrontEnd** **проекта service.yaml** под**сервисные ресурсы** **WebFrontEnd.** > 

    Обратите внимание, `resources:` что `cpu:` в `"[parameters('WebFrontEnd_cpu')]"`разделе, установлен на . Если проект строится для облака, значение `'WebFrontEnd_cpu` будет взято из**параметра** **Облаков** >  **сред.** >  `1.5` Если проект строится для локального запуска, значение будет взято из файла **Environments** > **Local** > **parameters.yaml** и будет "0,5".

> [!Tip]
> По умолчанию файл параметра, который является одноранговым файлом profile.yaml, будет использоваться для предоставления значений для этого файла profile.yaml.
> Например, параметры среды > облако>.yaml предоставляет значения параметров для сред > Cloud > profile.yaml.
>
> Вы можете переопределить это, добавив следующее в файл`parametersFilePath=”relative or full path to the parameters file”` profile.yaml: Например, `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` или`parametersFilePath=”..\CommonParameters.yaml”`

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

Если вы делаете обновление кода, или обновление конфигурации (в данном случае мы делаем и то, и другое), обновить приложение Service Fabric Mesh на Azure, нажав на **todolistapp** справа, а затем выберите **Publish...**

Дальше появится диалоговое окно **Публикация приложения Service Fabric**.

Используйте выпадение **целевого профиля,** чтобы выбрать файл profile.yaml для использования для этого развертывания. Мы модернизируем приложение в облаке, поэтому выбираем **cloud.yaml** в отсечке, которое будет использовать `WebFrontEnd_cpu` значение 1.0, определенное в этом файле.

![Диалоговое окно публикации службы "Сетка Service Fabric" в Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Выберите учетную запись Azure и подписку. Установите **местоположение** в место, которое использовалось при первоначальном опубликовании приложения для размещения в Azure. В этой статье используется **восточная часть США**.

Установите **группу ресурсов** в группу ресурсов, которую вы использовали при первоначальном опубликовании приложения для использования в Azure.

Установите **реестр контейнеров Azure** на имя реестра лазурных контейнеров, созданное при первоначальном опубликовании приложения для дел в Azure.

В диалоговом окне публикации нажмите кнопку **Опубликовать**, чтобы обновить приложение списка дел в Azure.

Следите за ходом обновления, выбрав панель **Service Fabric Tools** в окне **вывода** Visual Studio. 

После того, как изображение будет построено и перемещено в реестр контейнеров Azure, в выводе, который можно нажать, появится ссылка **«Для»** для мониторинга развертывания на портале Azure.

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