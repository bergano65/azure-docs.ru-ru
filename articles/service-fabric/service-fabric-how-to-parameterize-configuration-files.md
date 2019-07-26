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
ms.openlocfilehash: dad497978de7187177998524db3b2f2ee448c717
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464788"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Как параметризовать файлы конфигурации в Service Fabric

В этой статье описывается, как параметризовать файл конфигурации в Service Fabric.  Дополнительные сведения об основных концепциях см. в разделе [Управление приложениями для нескольких сред](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Процедура параметризации файлов конфигурации

В этом примере с помощью параметров в развертывании приложения переопределяется значение конфигурации.

1. Откройте файл MyService > \паккажерут\конфиг\сеттингс.ксмл в проекте службы.  *\<*
1. Задайте имя и значение параметра конфигурации, например кэш размером 25, добавив следующий XML:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Сохраните и закройте файл.
1. Откройте файл MyApplication > \аппликатионпаккажерут\аппликатионманифест.ксмл.  *\<*
1. В файле ApplicationManifest.xml объявите параметр и значение по умолчанию в элементе `Parameters`.  Рекомендуется задавать имя параметра с содержанием имени службы (например, "MyService").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. В разделе файла ApplicationManifest. XML `ConfigOverrides` добавьте элемент и `ConfigOverride` , ссылающийся на пакет конфигурации, раздел и параметр. `ServiceManifestImport`

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

## <a name="next-steps"></a>Следующие шаги
Сведения о других возможностях управления приложениями, доступными в Visual Studio, см. в статье [Использование Visual Studio для упрощения создания приложений Service Fabric и управления ими](service-fabric-manage-application-in-visual-studio.md).
