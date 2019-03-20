---
title: Параметризация файлов конфигурации в Azure Service Fabric | Документация Майкрософт
description: Узнайте, как параметризовать файлы конфигурации в Service Fabric.
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 0ab6e3f189d4a2e7e8f3bc96108d7979c99fffa8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102675"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Как параметризовать файлы конфигурации в Service Fabric

В этой статье описывается, как параметризовать файл конфигурации в Service Fabric.  Дополнительные сведения об основных концепциях см. в разделе [Управление приложениями для нескольких сред](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Процедура параметризации файлов конфигурации

В этом примере с помощью параметров в развертывании приложения переопределяется значение конфигурации.

1. Откройте файл *<MyService>\PackageRoot\Config\Settings.xml* в проекте службы.
1. Задайте имя и значение параметра конфигурации, например кэш размером 25, добавив следующий XML:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Сохраните и закройте файл.
1. Откройте файл *<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml*.
1. В файле ApplicationManifest.xml объявите параметр и значение по умолчанию в элементе `Parameters`.  Рекомендуется задавать имя параметра с содержанием имени службы (например, "MyService").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. В разделе `ServiceManifestImport`файла ApplicationManifest.xml добавьте элемент`ConfigOverride`, ссылаясь на пакет конфигурации, раздел и параметр.

   ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
   ```

> [!NOTE]
> В случае добавления ConfigOverride служба Service Fabric всегда выбирает параметры приложения или значение по умолчанию, указанное в манифесте приложения.
>
>

## <a name="next-steps"></a>Дальнейшие действия
Сведения о других возможностях управления приложениями, доступными в Visual Studio, см. в статье [Использование Visual Studio для упрощения создания приложений Service Fabric и управления ими](service-fabric-manage-application-in-visual-studio.md).