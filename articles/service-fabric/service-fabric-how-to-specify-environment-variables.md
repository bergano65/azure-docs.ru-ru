---
title: Указать переменные среды для служб
description: Показано, как использовать переменные среды для приложений в Service Fabric.
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: f4c4f2a1c140e3d0f181c4fd55482056f9f91b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614321"
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

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Определение переменных среды динамически с помощью Docker Compose

Service Fabric поддерживает возможность [использования Docker Compose для развертывания.](service-fabric-docker-compose.md#supported-compose-directives) Составные файлы могут создавать переменные среды из оболочки. Это поведение может быть использовано для динамического замены желаемых значений среды:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Дальнейшие действия
Чтобы узнать больше о некоторых основных понятиях, рассмотренных в данной статье, ознакомьтесь с разделом [Управление параметрами приложения для нескольких сред](service-fabric-manage-multiple-environment-app-configuration.md).

Сведения о других возможностях управления приложениями, доступными в Visual Studio, см. в статье [Использование Visual Studio для упрощения создания приложений Service Fabric и управления ими](service-fabric-manage-application-in-visual-studio.md).
