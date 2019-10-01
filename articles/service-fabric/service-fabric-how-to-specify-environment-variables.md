---
title: Как указать переменные среды для служб в Azure Service Fabric | Документация Майкрософт
description: Показано, как использовать переменные среды для приложений в Service Fabric.
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: df9b199c24301016b9f9da8a8dec52129bbf94bd
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703545"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Как указать переменные среды для служб в Service Fabric

В этой статье показано, как задать переменные среды для службы или контейнера в Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Процедуры указания переменных среды для служб

В этом примере задается значение переменной среды для контейнера. В статье предполагается, что у вас уже есть манифест приложения и манифест служб.

1. Откройте файл ServiceManifest.xml.
2. В элемент `CodePackage` добавьте новые элементы `EnvironmentVariables` и `EnvironmentVariable` для каждой переменной среды.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   Переменные среды можно переопределить в манифесте приложения.

3. Для этого используйте элемент `EnvironmentOverrides`.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Динамическое указание переменных среды с помощью Docker Compose

Service Fabric поддерживает возможность [использования DOCKER Compose для развертывания](service-fabric-docker-compose.md#supported-compose-directives). Создание файлов может быть источником переменных среды из оболочки. Это поведение можно использовать для динамического замены требуемых значений среды:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Следующие шаги
Чтобы узнать больше о некоторых основных понятиях, рассмотренных в данной статье, ознакомьтесь с разделом [Управление параметрами приложения для нескольких сред](service-fabric-manage-multiple-environment-app-configuration.md).

Сведения о других возможностях управления приложениями, доступными в Visual Studio, см. в статье [Использование Visual Studio для упрощения создания приложений Service Fabric и управления ими](service-fabric-manage-application-in-visual-studio.md).
