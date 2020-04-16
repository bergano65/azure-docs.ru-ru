---
title: Семантика RunToCompletion в сервисной ткани
description: Описывает семантику RunToCompletion в сервисной ткани.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: adf4b11412aa752144d4ed4fef06d2de1d76598d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431297"
---
# <a name="runtocompletion"></a>RuntoCompletion

Начиная с версии 7.1, Service Fabric поддерживает семантику **RunToCompletion** для [контейнеров][containers-introduction-link] и [исполняемых приложений.][guest-executables-introduction-link] Эти семантики позволяют приложенияи и службы, которые выполняют задачу и выходят, в отличие от всегда работающих приложений и служб.

Прежде чем приступить к этой статье, мы рекомендуем ознакомиться с [моделью приложения Service Fabric][application-model-link] и [моделью хостинга Service Fabric.][hosting-model-link]

> [!NOTE]
> Семантика RunToCompletion в настоящее время не поддерживается для служб, написанных с помощью модели программирования [Надежных Служб.][reliable-services-link]
 
## <a name="runtocompletion-semantics-and-specification"></a>Семантика и спецификация RunToCompletion
Семантика RunToCompletion может быть указана как **executionPolicy** при [импорте ServiceManifest.][application-and-service-manifests-link] Указанная политика наследуется всеми CodePackages, включающими ServiceManifest. Следующий фрагмент ApplicationManifest.xml приводит пример.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** допускает следующие два атрибута:
* **Тип:** **RunToCompletion** в настоящее время является единственным допустимое значение для этого атрибута.
* **Перезагрузка:** Этот атрибут определяет политику перезагрузки, которая применяется к CodePackages, включающим ServicePackage, на сбой. CodePackage, выход из него с **ненулевым кодом выхода,** считается неудачным. Разрешенные значения для этого атрибута **OnFailure** и **Никогда не** с **OnFailure** является по умолчанию.

При перезагрузке политики, установленной **на OnFailure,** если какой-либо CodePackage выходит из строя **(ненулевой код выхода),** он перезапускается, с откатами между повторными сбоями. При перезагрузке **политики,** установленной never , если какой-либо CodePackage не удается, состояние развертывания DeployedServicePackage помечается как **неудавшийся,** но другие CodePackages могут продолжить выполнение. Если все пакеты CodePackages, включающие в себя запуск ServicePackage, успешно **завершены (код выхода 0),** статус развертывания deployedServicePackage помечается как **RanToCompletion.** 

## <a name="complete-example-using-runtocompletion-semantics"></a>Полный пример с помощью семантики RunToCompletion

Давайте рассмотрим полный пример с помощью семантики RunToCompletion.

> [!IMPORTANT]
> Следующий пример предполагает знакомство с [созданием контейнерных приложений Windows с использованием Service Fabric и Docker.][containers-getting-started-link]
>
> Этот пример ссылается mcr.microsoft.com/windows/nanoserver:1809. Контейнеры Windows Server совместимы не со всеми версиями ОС узла. Дополнительные сведения см. в разделе [Совместимость версий контейнеров Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

Следующий ServiceManifest.xml описывает ServicePackage, состоящий из двух CodePackages, которые представляют контейнеры. *RunToCompletionCodePackage1* просто регистрирует сообщение для **stdout** и выходов. *RunToCompletionCodePackage2* пинги loopback адрес на некоторое время, а затем выходит с выходом код либо **0**, **1** или **2**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

Следующее ApplicationManifest.xml описывает приложение на основе упомянутого выше ServiceManifest.xml. Он определяет **RunToCompletion** **ExecutionPolicy** для *WindowsRunToCompletionServicePackage* с политикой перезапуска **OnFailure**. После активации *WindowsRunToCompletionServicePackage,* его составные CodePackages будут запущены. *RunToCompletionCodePackage1* должен успешно выйти при первой активации. Тем не менее, *RunToCompletionCodePackage2* может выйти из строя **(ненулевой код выхода),** и в этом случае он будет перезапущен, так как политика перезагрузки **OnFailure.**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>Запрос состояния развертывания развернутогоServiceпакета
Статус развертывания развернутогоServiceпакета можно запрашивать у PowerShell с помощью [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] или с помощью [API FabricClient][fabric-client-link] [GetDeployedServicePackageListAsync (String, Uri, String)][deployed-service-package-fabricclient-link]

## <a name="considerations-when-using-runtocompletion-semantics"></a>Рассмотрение при использовании семантики RunToCompletion

Следующие моменты следует отметить для текущей поддержки RunToCompletion.
* Эти семантики поддерживаются только для [контейнеров][containers-introduction-link] и [экспонесоваемых][guest-executables-introduction-link] приложений.
* Сценарии обновления приложений с семантикой RunToCompletion не допускаются. При необходимости пользователи должны удалять и воссоздавать такие приложения.
* События Failover могут привести к повторному выполнению CodePackages после успешного завершения на том же узлах или других узлах кластера. Примерами событий сбоя являются перезагрузка узлов и обновление времени выполнения Service Fabric на узлах.

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
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[fabric-client-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync

