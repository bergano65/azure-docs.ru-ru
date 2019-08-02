---
title: Отладка контейнеров Windows с помощью Service Fabric и VS | Документация Майкрософт
description: Узнайте, как отлаживать контейнеры Windows в Azure Service Fabric с помощью Visual Studio 2019.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 043623869163a27cdbbdd7115fca43486577ca7d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599442"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Практическое руководство. Отладка контейнеров Windows в Azure Service Fabric с помощью Visual Studio 2019

Visual Studio 2019 позволяет отлаживать приложения .NET в контейнерах как службы Service Fabric. В этой статье показано, как настроить среду, а затем выполнить отладку приложения .NET в контейнере, выполняемом в локальном кластере Service Fabric.

## <a name="prerequisites"></a>предварительные требования

* В Windows 10 выполните это краткое руководство, чтобы [настроить Windows 10 для запуска контейнеров Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10).
* В Windows Server 2016 выполните это краткое руководство, чтобы [настроить Windows 2016 для запуска контейнеров Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server).
* Настройте локальную среду Service Fabric, следуя указаниям из статьи [Настройка среды разработки для Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Настройка среды разработчика для отладки контейнеров

1. Убедитесь, что Docker для службы Windows выполняется, прежде чем переходить к следующему шагу.

1. Для поддержки разрешения DNS между контейнерами необходимо настроить локальный кластер разработки, используя имя компьютера. Также эти действия необходимо выполнить, если нужно обратиться к службам через обратный прокси-сервер.
   1. Откройте PowerShell от имени администратора.
   2. Откройте папку установки SDK кластера, как правило `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`.
   3. Запустите скрипт `DevClusterSetup.ps1`.

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > `-CreateOneNodeCluster` можно использовать, чтобы настроить кластер с одним узлом. По умолчанию будет создан локальный кластер с пятью узлами.
      >

      Дополнительные сведения о службе DNS в Service Fabric см. в [этой статье](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). Дополнительные сведения об использовании обратного прокси-сервера Service Fabric из служб, запущенных в контейнере, см. в разделе [Специальные действия для служб с общим доступом к портам](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Известные ограничения при отладке контейнеров в Service Fabric

Ниже приведен список известных ограничений отладки контейнеров в Service Fabric и возможные решения:

* Использование localhost для Клустерфкднорип не поддерживает разрешение DNS в контейнерах.
    * Способы устранения: Настройка локального кластера с помощью имени компьютера (см. выше).
* При выполнении Windows 10 на виртуальной машине ответ DNS не возвращается в контейнер.
    * Способы устранения: Отключить разгрузку контрольной суммы UDP для протокола IPv4 на сетевом адаптере Виртуальных машин.
    * Выполнение Windows 10 приведет к снижению производительности сети на компьютере.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Разрешение служб в одном приложении с использованием имени службы DNS не работает в Windows 10, если приложение было развернуто с помощью Docker Compose
    * Способы устранения: Используйте servicename.applicationname для разрешения конечных точек службы.
    * https://github.com/Azure/service-fabric-issues/issues/1062
* При использовании IP-адреса для ClusterFQDNorIP изменение основного IP-адреса на узле нарушит функциональность DNS.
    * Способы устранения: Повторно создайте кластер с использованием нового основного IP-адреса узла или используйте имя компьютера. Эта проблема связана с проектированием.
* Если полное доменное имя кластера, с которым был создан кластер, не разрешается в сети, DNS завершится ошибкой.
    * Способы устранения: Повторно создайте локальный кластер с использованием основного IP-адреса узла. Эта ошибка связана с проектированием.
* При отладке контейнера docker журналы будут доступны только в окне вывода Visual Studio, а не через API Service Fabric, включая Service Fabric Explorer.

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Отладка приложения .NET, работающего в контейнерах docker, в Service Fabric

1. Запустите Visual Studio от имени администратора.

1. Откройте имеющееся приложение .NET или создайте новое.

1. Щелкните проект правой кнопкой мыши и выберите **Добавить -> Поддержка оркестратора контейнеров -> Service Fabric**.

1. Нажмите клавишу **F5** для запуска отладки приложения.

    Visual Studio поддерживает типы консоли и проектов ASP.NET для .NET и .NET Core.

## <a name="next-steps"></a>Следующие шаги
Дополнительные сведения о возможностях Service Fabric и контейнеров см. в разделе Общие сведения о Service Fabric контейнерах] (Service-Fabric-Containers-overview.md).
