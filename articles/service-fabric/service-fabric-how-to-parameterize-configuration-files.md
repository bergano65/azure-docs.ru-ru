---
title: Параметрыйте файлы конфигурации в ткани службы Azure
description: Узнайте, как параметризировать файлы конфигурации в Service Fabric, полезный метод при управлении несколькими средами.
author: mikkelhegn
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 4e96a732cffd70b0a5c24e7ebafe214297a72720
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644636"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Как параметризовать файлы конфигурации в Service Fabric

В этой статье описывается, как параметризовать файл конфигурации в Service Fabric.  Дополнительные сведения об основных концепциях см. в разделе [Управление приложениями для нескольких сред](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Процедура параметризации файлов конфигурации

В этом примере с помощью параметров в развертывании приложения переопределяется значение конфигурации.

1. Откройте файл * \<MyService>-PackageRoot-Config-Settings.xml* в вашем сервисном проекте.
1. Задайте имя и значение параметра конфигурации, например кэш размером 25, добавив следующий XML:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Сохраните файл и закройте его.
1. Откройте файл * \<MyApplication>-ApplicationPackageRoot-ApplicationManifest.xml.*
1. В файле ApplicationManifest.xml объявите параметр и значение по умолчанию в элементе `Parameters`.  Рекомендуется задавать имя параметра с содержанием имени службы (например, "MyService").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. В `ServiceManifestImport` разделе файла ApplicationManifest.xml добавьте элемент `ConfigOverrides` и `ConfigOverride` элемент, ссылаясь на пакет конфигурации, раздел и параметр.

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
