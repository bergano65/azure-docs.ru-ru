---
title: Дополнительные сведения о создании и публикации.Net Core приложения на удаленном кластере Azure Service Fabric для Linux | Документация Майкрософт
description: Создание и публикация.Net Core приложения, предназначенные для удаленного кластера Linux из Visual Studio
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: 46d76edbe8cede12e8c7811f43c28a65c1ebaed0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078668"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Используйте Visual Studio для создания и публикации.Net Core приложений, ориентированных на удаленный кластер Service Fabric для Linux
С помощью Visual Studio вам можно разрабатывать и публиковать.Net Core Service Fabric приложения, предназначенные для кластера Service Fabric для Linux. Версия пакета SDK должен быть 3.4 или более поздней версии для развертывания.Net Core приложение, предназначенное для Linux Service Fabric кластеров под управлением Windows из Visual Studio.

> [!Note]
> Visual Studio не поддерживает отладку приложений Service Fabric для Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Создание приложения Service Fabric, предназначенных для.Net Core
1. Запустите Visual Studio от имени **администратора**.
2. Создание проекта с **файл -> Создать -> проект**.
3. В **новый проект** диалоговом окне выберите **Cloud -> приложение Service Fabric**.
![Создание приложения]
4. Имя приложения и нажмите кнопку **ОК**.
5. На **новая служба Service Fabric** выберите тип службы, которые вы хотите создать в рамках **.Net Core разделе**.
![Создание службы]

## <a name="deploy-to-a-remote-linux-cluster"></a>Развертывание на удаленный кластер Linux
1. В обозревателе решений щелкните правой кнопкой мыши приложение и выберите **построения**.
![построение приложения]
2. После завершения процесса построения для приложения, щелкните правой кнопкой службу и выберите Изменить **CSPROJ-файле**.
![изменить csproj]
3. Изменение свойства UpdateServiceFabricManifestEnabled из значение True, чтобы **False** Если служба **тип проекта субъекта**. Если приложение не имеет службы субъектов, перейдите к шагу 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Задать UpdateServiceFabricManifestEnabled false, будет отключить обновления файл ServiceManifest.xml во время сборки. Любое изменение таких как добавление, удаление или переименование к службе не отражаются в файле ServiceManifest.xml. При внесении изменений необходимо либо обновить ServiceManifest вручную или временно задать UpdateServiceFabricManifestEnabled равным true и создания службы, которая обновит файл ServiceManifest.xml и затем вернуть ее обратно значение false.
>

4. Обновите RuntimeIndetifier из Windows 7 — x64 на целевую платформу в проекте службы.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. В ServiceManifest обновите программу entrypoint, чтобы удалить .exe. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. В обозревателе решений щелкните правой кнопкой мыши приложение и выберите **публикации**. Откроется диалоговое окно **Publish** (Опубликовать).
7. В **конечная точка подключения**, выберите конечную точку для удаленного кластера Service Fabric для Linux, который будут являться целевыми.
![Публикация приложения]

<!--Image references-->
[Создание приложения]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[Создание службы]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[построение приложения]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[изменить csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[Публикация приложения]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о [Приступая к работе с Service Fabric с помощью.Net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
