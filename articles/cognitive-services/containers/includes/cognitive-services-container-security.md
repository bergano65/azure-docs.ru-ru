---
title: Безопасность контейнера
titleSuffix: Azure Cognitive Services
description: Узнайте, как защитить контейнер
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: dapine
ms.openlocfilehash: d8d069dddbce6ab6ddb541db460634ad3f6fa067
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994959"
---
## <a name="azure-cognitive-services-container-security"></a>Безопасность контейнера Cognitive Services Azure

Безопасность должна быть основной задачей при разработке приложений. Важность безопасности является метрикой успеха. При проектировании программного решения, включающего Cognitive Services контейнеры, крайне важно понимать ограничения и возможности, доступные вам. Дополнительные сведения см. в статье [Безопасность Azure][az-security].

> [!IMPORTANT]
> По умолчанию в API контейнера Cognitive Services *нет безопасности* . Это связано с тем, что чаще всего контейнер будет работать как часть Pod, которая защищена извне сетевым мостом. Однако можно включить проверку подлинности, которая работает аналогично проверке подлинности, используемой при доступе к [облачной Cognitive Services][request-authentication].

На схеме ниже показан подход, используемый по умолчанию и **небезопасный** .

![Безопасность контейнера](../media/container-security.svg)

В качестве альтернативного и *безопасного* подхода потребители Cognitive Services контейнеры могут расширить контейнер с интерфейсным компонентом, сохранив конечную точку контейнера закрытой. Рассмотрим ситуацию, когда мы используем [Istio][istio] в качестве входящего шлюза. Istio поддерживает аутентификацию по протоколам HTTPS/SSL и Client-Certificate. В этом сценарии интерфейс Istio предоставляет доступ к контейнеру, представляя сертификат клиента, список разрешений заранее с Istio.

[Nginx][nginx] — это еще один популярный вариант в той же категории. Как Istio, так и nginx выступают в качестве сетки службы и предлагают дополнительные функции, такие как балансировка нагрузки, маршрутизация и контроль скорости.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../../security/fundamentals/overview.md
