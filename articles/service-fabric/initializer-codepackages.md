---
title: Инициайзер CodePackages в сервисной ткани
description: Описывает Инициализатор CodePackages в сервисной ткани.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 8483e00f55d0dd49ba57db58b99b237ce0a169e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430634"
---
# <a name="initializer-codepackages"></a>Инициайзер CodePackages

Начиная с версии 7.1, Service Fabric поддерживает **Инициайзер CodePackages** для [контейнеров][containers-introduction-link] и [экспоненируемых][guest-executables-introduction-link] приложений для гостей. Инициайзер CodePackages предоставляет возможность выполнять инициализации в сфере ServicePackage до начала выполнения других CodePackages. Их отношение к ServicePackage аналогично тому, что [SetupEntryPoint][setup-entry-point-link] для CodePackage.

Прежде чем приступить к этой статье, мы рекомендуем ознакомиться с [моделью приложения Service Fabric][application-model-link] и [моделью хостинга Service Fabric.][hosting-model-link]

> [!NOTE]
> В настоящее время CodePackages Initializer не поддерживается для служб, написанных с использованием модели программирования [Надежных Услуг.][reliable-services-link]
 
## <a name="semantics"></a>Семантика

Ожидается, что Инициайзер CodePackage будет успешно **завершен (код выхода 0).** Неудачный Инициайзер CodePackage перезапускается до тех пор, пока он успешно не завершится. Несколько Инициайзер CodePackages разрешены и выполняются до **успешного завершения,** **последовательно**, **в определенном порядке,** прежде чем другие CodePackages в ServicePackage начать выполнение.

## <a name="specifying-initializer-codepackages"></a>Указание Кодпакетов инициатора
Вы можете пометить CodePackage как инициатор, установив атрибут **Инициализатора** на **истину** в ServiceManifest. При наличии нескольких CodePackages Инициайзера их порядок выполнения может быть указан через атрибут **ExecOrder.** **ExecOrder** должен быть неотрицательным рядом и действителен только для Инициайзера CodePackages. Первоначальный кодпакеты с более низкими значениями **ExecOrder** выполняются в первую очередь. Если **ExecOrder** не указан для CodePackage Инициайзера, предполагается значение по умолчанию 0. Относительный порядок исполнения Инициайзера CodePackages с той же стоимостью **ExecOrder** не указан.

Следующий фрагмент ServiceManifest описывает три CodePackages, два из которых помечены как инициаторы. Когда этот Пакет услуг активирован, *InitCodePackage0* выполняется сначала, так как он имеет наименьшее значение **ExecOrder.** При успешном завершении (код выхода 0) *InitCodePackage0*, *InitCodePackage1* выполняется. Наконец, при успешном завершении *InitCodePackage1,* *WorkloadCodePackage* выполняется.

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>Полный пример с использованием CodePackages Инициайзера

Давайте рассмотрим полный пример с помощью Инициайзер CodePackages.

> [!IMPORTANT]
> Следующий пример предполагает знакомство с [созданием контейнерных приложений Windows с использованием Service Fabric и Docker.][containers-getting-started-link]
>
> Этот пример ссылается mcr.microsoft.com/windows/nanoserver:1809. Контейнеры Windows Server совместимы не со всеми версиями ОС узла. Дополнительные сведения см. в разделе [Совместимость версий контейнеров Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

Следующий ServiceManifest.xml основывается на фрагменте ServiceManifest, описанном ранее. *InitCodePackage0*, *InitCodePackage1* и *WorkloadCodePackage* являются CodePackages, которые представляют контейнеры. После *активации, InitCodePackage0* выполняется первым. Он регистрирует сообщение в файл и выходит. Далее выполняется *InitCodePackage1,* который также регистрирует сообщение в файл и выходит. Наконец, *WorkloadCodePackage* начинает выполнение. Он также регистрирует сообщение в файл, выводит содержимое файла для **stdout,** а затем пинги навсегда.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

Следующее ApplicationManifest.xml описывает приложение на основе упомянутого выше ServiceManifest.xml. Обратите внимание, что он определяет одно и то же крепление **объема** для всех контейнеров, т.е. **C:'WorkspaceOnHost** устанавливается на **C:'WorkspaceOnContainer** на всех трех контейнерах. Чистый эффект заключается в том, что все контейнеры пишут в один и тот же файл журнала в том порядке, в котором они активированы.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
После успешной активации ServicePackage содержимое **C: «WorkspaceOnHost»log.txt** должно быть следующим.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

## <a name="next-steps"></a>Дальнейшие действия

Смотрите следующие статьи для соответствующей информации.

* [Сервисная ткань и контейнеры.][containers-introduction-link]
* [СервисНая ткань и гостевые исполнители.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md

