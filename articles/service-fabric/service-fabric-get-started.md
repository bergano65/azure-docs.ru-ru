---
title: Настройка среды разработки Windows для микрослужб Azure | Документация Майкрософт
description: Установите среду выполнения, пакет SDK и инструменты и создайте локальный кластер разработки. После завершения установки вы сможете создавать приложения на базе Windows.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2018
ms.author: ryanwi
ms.openlocfilehash: 968c9f2455269cf41a701de685e4f47255b46847
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53386434"
---
# <a name="prepare-your-development-environment-on-windows"></a>Настройка среды разработки для Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

Чтобы создавать и запускать [приложения Service Fabric][1] на компьютере для разработки Windows, установите среду выполнения Service Fabric, пакет SDK и инструменты. Вам также нужно [включить выполнение сценариев Windows PowerShell](#enable-powershell-script-execution), включенных в пакет SDK.

## <a name="prerequisites"></a>Предварительные требования
### <a name="supported-operating-system-versions"></a>Поддерживаемые версии операционных систем
Для разработки поддерживаются следующие операционные системы:

* Windows 7
* Windows 8 и Windows 8.1;
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Поддержка Windows 7:
> - Windows 7 по умолчанию поставляется с Windows PowerShell версии 2.0. Для выполнения командлетов PowerShell для Service Fabric требуется PowerShell начиная с версии 3.0. В центре загрузки Майкрософт можно [скачать Windows PowerShell 5.0][powershell5-download].
> - Обратный прокси-сервер Service Fabric недоступен в Windows 7.
>

## <a name="install-the-sdk-and-tools"></a>Установка пакета SDK и инструментов
Установщик веб-платформы (WebPI) — это рекомендуемый способ установки пакета SDK и средств. При получении ошибок, при выполнения с помощью установщика веб-платформы, также можно найти прямые ссылки на средства установки в заметках о выпуске для определенной версии Service Fabric. Заметки о выпуске можно найти в различных объявлениях о выпусках в [блоге команды Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

### <a name="to-use-visual-studio-2017"></a>Для использования Visual Studio 2017
Средства Service Fabric являются частью рабочей нагрузки разработки Azure в Visual Studio 2017. Эту рабочую нагрузку необходимо включить при установке Visual Studio.
Кроме того, необходимо установить пакет SDK и среду выполнения Microsoft Azure Service Fabric, используя установщик веб-платформы.

* [Установка пакета SDK Microsoft Azure Service Fabric][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Для использования Visual Studio 2015 (требуется Visual Studio 2015 с обновлением 2 или более поздней версии)
Для Visual Studio 2015 средства Service Fabric устанавливаются вместе с пакетом SDK и средой выполнения с помощью установщика веб-платформы:

* [Установка пакета SDK и средств Microsoft Azure Service Fabric][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Только установка пакета SDK
Если вам требуется только пакет SDK, можно установить этот пакет:
* [Установка пакета SDK Microsoft Azure Service Fabric][core-sdk]

Текущие версии:
* пакет SDK и Средства Service Fabric версии 3.3.617;
* среда выполнения Service Fabric 6.4.617;
* Средства Service Fabric для Visual Studio 2015 2.4.11116.1;
* среда Visual Studio 2017 15.9, которая включает в себя Средства Service Fabric для Visual Studio 2.4.11024.1. 

Список поддерживаемых версий см. в статье [Azure Service Fabric support options](service-fabric-support.md) (Варианты поддержки Azure Service Fabric).

> [!NOTE]
> Кластер одной машины (OneBox) не поддерживаются для обновлений приложений или кластеров. Удалите кластер OneBox и заново создайте его при необходимости обновления кластера или возникли проблемы с обновлением приложения. 

## <a name="enable-powershell-script-execution"></a>Включение сценариев PowerShell
Для создания локального кластера разработки и развертывания приложений из Visual Studio в Service Fabric используются сценарии Windows PowerShell. По умолчанию ОС Windows блокирует выполнение этих сценариев. Чтобы включить их, необходимо изменить политику выполнения PowerShell. Для этого запустите PowerShell с правами администратора и введите следующую команду:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```
## <a name="install-docker-optional"></a>Установка Docker (необязательно)
[Service Fabric — это оркестратор контейнеров](service-fabric-containers-overview.md) для развертывания микрослужб в кластере компьютеров. Для запуска приложений контейнера Windows на локальном кластере разработки необходимо сначала установить Docker для Windows. Скачайте [Docker CE для Windows (стабильная версия)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). После установки и запуска Docker щелкните правой кнопкой мыши значок в области уведомлений и выберите **Switch to Windows containers** (Переключиться на контейнеры Windows). Это необходимо для запуска образов Docker на базе Windows.

## <a name="next-steps"></a>Дополнительная информация
Среда разработки настроена, и вы готовы к созданию и запуску собственных приложений.

* [Создание первого приложения Service Fabric в Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
* [Информация о развертывании приложений в локальном кластере и управлении ими](service-fabric-get-started-with-a-local-cluster.md)
* [Общие сведения о модели программирования Service Fabrics](service-fabric-choose-framework.md)
* [Ознакомление с примерами кода Service Fabric на GitHub](https://aka.ms/servicefabricsamples)
* [Визуализация кластера с помощью обозревателя Service Fabric](service-fabric-visualizing-your-cluster.md)
* [Сведения о вариантах поддержки Service Fabric](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Страница кампании Service Fabric"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Ссылка на WebPI VS 2015"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Ссылка на WebPI Dev15"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Ссылка на WebPI Core SDK"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
