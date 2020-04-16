---
title: Зонды Azure Service Fabric
description: Как смоделировать liveness Probe в Azure Service Fabric с помощью файлов приложений и сервисных манифестов.
ms.topic: conceptual
ms.date: 3/12/2020
ms.openlocfilehash: 38f3888a29bf505b723d40bc7cd08fb0c7e29eff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431219"
---
# <a name="liveness-probe"></a>Зонд живости
Начиная с 7.1 Service Fabric поддерживает механизм зонда Liveness для [контейнерных][containers-introduction-link] приложений. Liveness Probe поможет объявить о живости контейнерного приложения, и когда они не отвечают своевременно, это приведет к перезагрузке.
В этой статье приводится обзор того, как определить зонд Liveness с помощью файлов manifest.

Прежде чем приступить к этой статье, мы рекомендуем ознакомиться с [моделью приложения Service Fabric][application-model-link] и [моделью хостинга Service Fabric.][hosting-model-link]

> [!NOTE]
> Зонд Liveness поддерживается только для контейнеров в режиме сети NAT.

## <a name="semantics"></a>Семантика
Вы можете указать только 1 зонд Liveness на контейнер и управлять его поведением с помощью этих полей:

* `initialDelaySeconds`: Первоначальная задержка в секундах, чтобы начать выполнение зонда после запуска контейнера. Поддерживаемое значение int. По умолчанию 0. Минимум 0.

* `timeoutSeconds`: Период в секундах, после которого мы считаем зонд неудачным, если он не завершил успешно. Поддерживаемое значение int. По умолчанию 1. Минимальное значение — 1.

* `periodSeconds`: Период в секундах, чтобы указать, как часто мы зондирования. Поддерживаемое значение int. По умолчанию 10. Минимальное значение — 1.

* `failureThreshold`: Как только мы нажмем FailureThreshold, контейнер будет перезазапуск. Поддерживаемое значение int. По умолчанию 3. Минимальное значение — 1.

* `successThreshold`: На неудачу, для зонда, чтобы считаться успешным он должен выполнить успешно для SuccessThreshold. Поддерживаемое значение int. По умолчанию 1. Минимальное значение — 1.

Там будет не более 1 зонд в контейнер в один момент времени. Если зонд не завершается в **тайм-аутSeconds** мы продолжаем ждать и считать его к **failureThreshold**. 

Кроме того, ServiceFabric поднимет следующие [отчеты о здоровье][health-introduction-link] зонда на DeployedServicePackage:

* `Ok`: Если зонд удается **для успехаThreshold** то мы сообщаем здоровья, как ok.

* `Error`: Если сбой зондаCount q ' **' failureThreshold**, перед перезапуском контейнера мы сообщаем Об ошибке.

* `Warning`: 
    1. Если зонд выходит из строя и сбойCount < **сбойThreshold** мы сообщаем Предупреждение. Этот отчет о работоспособности остается до тех пор, пока сбойCount не достигнет **сбояThreshold** или **successThreshold.**
    2. О успехе после неудачи, мы по-прежнему сообщают Предупреждение, но с обновленным последовательным успехом.

## <a name="specifying-liveness-probe"></a>Определение живости зонд

Вы можете указать зонд в ApplicationManifest.xml в сервисном ServiceManifestImport:

Зонд может либо один из:

1. HTTP
2. TCP
3. Exec 

## <a name="http-probe"></a>Зонд HTTP

Для зонда HTTP Service Fabric отправит запрос HTTP в указанный порт и указанный путь. Код возврата, превышающее или равное 200 и менее 400, указывает на успех.

Вот пример того, как указать зонд HttpGet:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

Зонд HttpGet имеет дополнительные свойства, которые можно установить:

* `path`: Путь к доступу к запросу HTTP.

* `port`: Порт для доступа для зондов. Диапазон от 1 до 65535. Mandatory.

* `scheme`: Схема для подключения к пакету кода. Если установлен на HTTPS, проверка сертификата пропускается. По умолчанию к HTTP

* `httpHeader`: Заголовки для установки в запросе. Вы можете указать несколько из них.

* `host`: Хост IP для подключения к.

## <a name="tcp-probe"></a>Зонд TCP

Для зонда TCP Service Fabric попытается открыть розетку на контейнере с указанным портом. Если он может установить соединение, зонд считается успешным. Вот пример того, как указать зонд, который использует розетку TCP:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="exec-probe"></a>Зонд Exec

Этот зонд выдаст exec в контейнер и будет ждать завершения команды.

> [!NOTE]
> Команда Exec берет запятую, отменяемую строку. Следующая команда в примере будет работать для контейнера Linux.
> Если вы пытаетесь контейнер windows, используйте <Command>cmd</Command>

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>Дальнейшие действия
Смотрите следующие статьи для соответствующей информации.
* [Сервисная ткань и контейнеры.][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

