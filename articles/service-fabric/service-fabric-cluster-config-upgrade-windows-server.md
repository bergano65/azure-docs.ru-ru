---
title: Обновление конфигурации изолированного кластера Azure Service Fabric | Документация Майкрософт
description: Узнайте, как обновить конфигурацию, которую использует изолированный кластер Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 88846845f1f8ffc71fb193e134a18ec38f619141
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51857879"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Обновление конфигурации изолированного кластера 

Для любой современной системы возможность обновления является ключом к успеху вашего продукта в долгосрочной перспективе. Кластер Azure Service Fabric — это ресурс, которым владеете вы. Из этой статьи вы узнаете, как обновить параметры конфигурации для изолированного кластера Service Fabric.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Настройка параметров кластера в файле ClusterConfig.json
Изолированные кластеры настраиваются с помощью файла *ClusterConfig.json*. Дополнительные сведения о различных параметрах см. в статье [Параметры конфигурации для изолированного кластера Windows](service-fabric-cluster-manifest.md).

В файле *ClusterConfig.json* в разделе `fabricSettings` [свойств кластера](./service-fabric-cluster-manifest.md#cluster-properties) можно добавлять, изменять и удалять параметры. 

Например, следующий JSON добавляет новый параметр *MaxDiskQuotaInMB* в разделе *Diagnostics* в строке `fabricSettings`:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Изменив параметры в файле ClusterConfig.json, [протестируйте конфигурацию кластера](#test-the-cluster-configuration), после чего [примените новые параметры](#upgrade-the-cluster-configuration), чтобы обновить конфигурацию. 

## <a name="test-the-cluster-configuration"></a>Тестирование конфигурации кластера
Прежде чем начинать обновление конфигурации, можно протестировать новый JSON-файл конфигурации кластера, выполнив следующий скрипт PowerShell в изолированном пакете:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Также можно выполнить этот скрипт:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Некоторые конфигурации невозможно обновить, например конечные точки, имена кластеров, IP-адреса узлов и т. д. Мы протестируем новый JSON-файл конфигурации кластера, используя старый файл. При наличии каких-либо проблем в окне PowerShell отобразятся сведения об ошибках.

## <a name="upgrade-the-cluster-configuration"></a>Обновление конфигурации кластера
Чтобы обновить конфигурацию кластера, выполните команду [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Обновление конфигурации обрабатывает домен обновления.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Обновление конфигурации сертификата кластера
Сертификат кластера используется для аутентификации между узлами кластера. Смену сертификата следует выполнять с особой осторожностью, так как в случае сбоя заблокируется связь между узлами кластера.

Поддерживаются четыре способа:  

* Однократное обновление сертификата. Путь обновления: ''сертификат А (основной) -> сертификат B (основной) -> сертификат C (основной) ->…''.

* Двойное обновление сертификата. Путь обновления ''сертификат А (основной) -> сертификат А (основной) и В (дополнительный) -> сертификат В (основной) -> сертификат В (основной) и С (дополнительный) -> сертификат C (основной)->…''.

* Тип обновления сертификатов: сертификат на основе отпечатка <-> сертификаты на основе конфигурации CommonName. Например, отпечаток сертификата A (основной) и отпечаток B (дополнительный) -> CommonName сертификата C.

* Обновление отпечатка издателя сертификата. Путь обновления "общее имя сертификата — A, отпечаток издателя — IT1 (основной) > общее имя сертификата — A, отпечаток издателя — IT1, IT2 (основной) > общее имя сертификата — A, отпечаток издателя — IT2 (основной)".


## <a name="next-steps"></a>Дополнительная информация
* Узнайте, как настроить некоторые [параметры кластера Service Fabric](service-fabric-cluster-fabric-settings.md).
* Ознакомьтесь с концепцией [масштабирования кластера](service-fabric-cluster-scale-up-down.md).
* Узнайте об [обновлениях приложений](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
