---
title: Создание и публикация a.Net базового приложения для удаленного кластера Linux
description: Создание и публикация приложений .Net Core, ориентированных на удаленный кластер Linux от Visual Studio
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614355"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Используйте Visual Studio для создания и публикации приложений .Net Core, ориентированных на удаленный кластер Linux Service Fabric
С помощью инструментов Visual Studio вы можете разрабатывать и публиковать приложения Service Fabric .Net Core, ориентированные на кластер Linux Service Fabric. Версия SDK должна быть 3.4 или выше для развертывания приложения .Net Core, ориентированного на кластеры Linux Service Fabric от Visual Studio.

> [!Note]
> Visual Studio не поддерживает отладку приложений Service Fabric, ориентированных на Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Создание приложения Service Fabric, ориентированного на ядро .Net
1. Запустите Visual Studio от имени **администратора**.
2. Создайте проект с **помощью проекта New->File >.**
3. В диалоге **нового проекта** выберите приложение **Cloud -> Service Fabric.**
![создать-приложение]
4. Назовите приложение и нажмите **Ok**.
5. На странице **New Service Fabric Service** выберите тип услуг, которые вы хотели бы создать в разделе **.Net Core**.
![создать сервис]

## <a name="deploy-to-a-remote-linux-cluster"></a>Развертывание в удаленном кластере Linux
1. В решении explorer, право нажмите на приложение и выберите **Build**.
![сборка-приложение]
2. После завершения процесса сборки приложения нажмите на службу и выберите отображдение **файла csproj.**
![edit-csproj]
3. Отспособите свойство UpdateServiceFabricManifestEnabled от True к **False,** если служба является **типом проекта актера.** Если в приложении нет службы актера, перейдите к шагу 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Установка UpdateServiceFabricManifestНа ложное, отсванет обновления для ServiceManifest.xml во время сборки. Любые изменения, такие как добавление, удаление или переименование в службу, не будут отражены в ServiceManifest.xml. Если какие-либо изменения внесены, вы должны либо обновить ServiceManifest вручную или временно установить UpdateServiceFabricManifest, чтобы исправить и построить службу, которая будет обновлять ServiceManifest.xml, а затем вернуть его обратно в ложный.
>

4. Обновление RuntimeIndetifier с win7-x64 на целевую платформу в проекте обслуживания.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. В ServiceManifest обновите программу входа для удаления .exe. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. В Solution Explorer, право нажмите на приложение и выберите **Опубликовать**. Откроется диалоговое окно **Publish** (Опубликовать).
7. В **endpoint Connection**выберите конечную точку для удаленного кластера Service Fabric Linux, на который вы хотели бы настроить таргетинг.
![публикация-заявка]

<!--Image references-->
[создать-приложение]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[создать сервис]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[сборка-приложение]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[публикация-заявка]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте о том, как [начать работу с сервисной тканью с помощью .Net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
