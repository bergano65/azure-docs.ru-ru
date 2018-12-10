---
title: Настройка среды разработки Windows для создания приложений Сетки Service Fabric | Документация Майкрософт
description: Настройте среду разработки Windows таким образом, чтобы можно было создавать приложение Сетки Service Fabric и разворачивать его в Сетке Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 11/29/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: bec0b9a7e34f1577f80a99f5380795c479c04bc8
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890472"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Чтобы создавать приложения Сетки Service Fabric, настройте среду разработки Windows

Чтобы создавать и запускать приложения Сетки Azure Service Fabric на компьютере для разработки Windows, установите среду выполнения Сетки Service Fabric, пакет SDK и инструменты.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Поддерживаемые версии операционных систем

Для разработки поддерживаются следующие операционные системы:

* Windows 10 (Корпоративная, Профессиональная или Образование)
* Windows Server 2016

## <a name="visual-studio"></a>Visual Studio

Для развертывания приложений сетки Service Fabric требуется Visual Studio 2017. [Его версия должна быть не ниже 15.6.0][download-visual-studio] и включать следующие рабочие нагрузки.

* ASP.NET и веб-разработка.
* разработка Azure.

## <a name="install-docker"></a>Установка Docker

#### <a name="windows-10"></a>Windows 10

Скачайте и установите последнюю версию [Docker Community Edition для Windows][download-docker] для поддержки контейнерных приложений Service Fabric, используемого Сеткой Service Fabric.

Во время процесса установки при появлении запроса выберите **Использовать контейнеры Windows вместо контейнеров Linux**.

Если на компьютере не включен Hyper-V, при установке Docker его будет предложено включить. Если будет предложено, щелкните **OK**.

#### <a name="windows-server-2016"></a>Windows Server 2016

Если у вас не включена роль Hyper-V, откройте PowerShell как администратор и выполните следующую команду, а затем перезагрузите компьютер. Дополнительные сведения см. в статье [Install Docker Enterprise Edition for Windows Server][download-docker-server] (Установка Docker Enterprise Edition для Windows).

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Перезагрузите компьютер.

Чтобы установить Docker, запустите PowerShell как администратор и выполните следующую команду.

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>Пакет SDK и средства

Установите Сетку Service Fabric, среду выполнения, пакет SDK и средства в следующем порядке.

1. Установите [пакет SDK Сетки Service Fabric][download-sdkmesh] с помощью установщика веб-платформы. Это позволит установить пакет SDK, средства Microsoft Azure Service Fabric и среду выполнения.
2. Установите [расширение средств службы "Сетка Service Fabric" в Visual Studio (предварительная версия)][download-tools] из Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Создание кластера

> [!IMPORTANT]
> Перед созданием кластера **необходимо** запустить Docker.
> Проверьте, работает ли Docker, открыв окно терминала и выполнив команду `docker ps`, которая укажет, возникает ли ошибка. Если полученный ответ не является ошибкой, это значит что Docker работает и можно начинать создание кластера.

Если используется Visual Studio, этот раздел можно пропустить, поскольку Visual Studio создает локальный кластер при его отсутствии.

Для повышения производительности во время процесса отладки при создании и запуске приложений Service Fabric рекомендуется создать кластер локальной разработки с одним узлом. Этот кластер должен быть запущен при развертывании или при отладке проекта Сетки Service Fabric.

После установки среды выполнения, пакета SDK, средства Visual Studio, Docker и запуска Docker создайте кластер разработки.

1. Закройте окно PowerShell.
2. Откройте окно PowerShell с повышенными правами от имени администратора. Этот шаг необходим для загрузки модулей Service Fabric, которые были недавно установлены.
3. Чтобы создать кластер разработки, выполните следующую команду PowerShell.

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. Чтобы запустить локальное средство управления кластером, выполните следующую команду PowerShell.

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. Когда средство управления кластером запущено (отображается на панели задач), щелкните его правой кнопкой мыши и выберите **Start Local Cluster** (Запустить локальный кластер).

![Рис. 1. Запуск локального кластера](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Теперь можно приступать к созданию приложений Сетки Service Fabric.

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с руководством [Tutorial: Create, debug, and deploy a multi-service web application to Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md) (Руководство. Создание, отладка и развертывание многосервисного веб-приложения в Сетке Service Fabric).

Найдите ответы на [часто задаваемые вопросы](service-fabric-mesh-faq.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/