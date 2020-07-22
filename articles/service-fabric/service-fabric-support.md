---
title: Сведения о вариантах поддержки Azure Service Fabric
description: Поддерживаемые версии кластера Service Fabric и ссылки для отправки запросов в службу поддержки.
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: ae49a59c2629d9f9461d298ada555d314c0c9f22
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86256974"
---
# <a name="azure-service-fabric-support-options"></a>Варианты поддержки Azure Service Fabric

Чтобы обеспечить надлежащую поддержку кластеров Service Fabric, выполняющих рабочие нагрузки ваших приложений, мы подготовили для вас различные варианты. Вы можете выбрать соответствующие варианты в зависимости от нужного уровня поддержки и серьезности проблемы. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Сообщение о проблемах рабочей среды или запрос платной поддержки для Azure

Чтобы сообщить о проблемах в кластере Service Fabric, развернутом в Azure, откройте запрос в службу поддержки [на портале Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) или [портале службы поддержки Майкрософт](https://support.microsoft.com/oas/default.aspx?prid=16146).

См. также:
 
- [Поддержка корпорации Майкрософт для клиентов Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Поддержка Premier](https://support.microsoft.com/en-us/premier).

> [!Note]
> Кластеры с бронзовым уровнем надежности или кластер с одним узлом позволяют выполнять только тестовые рабочие нагрузки. При возникновении проблем с кластером с бронзовым уровнем надежности или кластером с одним узлом техническая поддержка Майкрософт поможет устранить неполадку, но анализ первопричин проводить не будет. Дополнительные сведения см. в разделе [Характеристики надежности кластера](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster).
>
> Дополнительные сведения о том, что требуется для пригодного для промышленной эксплуатации кластера, см. в статье [Контрольный список готовности рабочей среды](./service-fabric-production-readiness-checklist.md).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Сообщение о проблемах рабочей среды или запрос платной поддержки для изолированных кластеров Service Fabric

Чтобы сообщить о проблемах в кластере Service Fabric, развернутом в локальной среде или облаке стороннего производителя, откройте запрос в профессиональную службу поддержки на [портале службы поддержки Майкрософт](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

См. также:

- [Professional Support Options](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0) (Варианты профессиональной поддержки).
- [Поддержка Premier](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Сообщение о проблемах Azure Service Fabric

Мы настроили репозиторий GitHub для сообщения о проблемах Service Fabric.  Кроме того, мы активно наблюдаем за приведенными ниже форумами.

### <a name="github-repo"></a>Репозиторий GitHub 

Сообщайте о проблемах Azure Service Fabric посредством [репозитория GitHub Service-Fabric-issues](https://github.com/Azure/service-fabric-issues). Этот репозиторий предназначен для сообщения о проблемах в Azure Service Fabric и их отслеживания, а также для выполнения запросов небольших функций. **Не используйте его для сообщения о проблемах в работе активных сайтов**.

### <a name="stackoverflow-and-msdn-forums"></a>Сайт StackOverflow и форум MSDN

[Тег Service Fabric на сайте StackOverflow][stackoverflow] и [форум MSDN по Service Fabric][msdn-forum] очень удобно использовать, чтобы задавать вопросы о принципах работы платформы и о том, как выполнять различные задачи с ее помощью.

### <a name="azure-feedback-forum"></a>Форум отзывов и предложений по Azure

[Форум отзывов и предложений по Azure Service Fabric][uservoice-forum] — это лучшее место, где можно поделиться идеями о важных функциях для продукта. В процессе среднесрочного и долгосрочного планирования мы изучаем наиболее популярные запросы. Мы рекомендуем вам обращаться с предложениями в службу поддержки в этом сообществе.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Предварительные версии Service Fabric не поддерживают работу в рабочей среде.

Время от времени мы выпускаем версии с важными функциями, доступными в виде предварительных версий, отзывы по которым мы хотим получить. Эти предварительные версии следует использовать только для целей тестирования. На производственном кластере всегда должна быть запущена поддерживаемая стабильная версия Service Fabric. Предварительная версия всегда начинается с номера основной и вспомогательной версии, равного 255. Например, если отображается версия Service Fabric 255.255.5703.949, она является предварительной и ее следует использовать в тестовых кластерах. О выходе этих предварительных выпусков также объявляется в [блоге группы Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) и приводятся сведения о функциях, включенных в их состав.
Возможность платной технической поддержки для этих предварительных выпусков отсутствует. Чтобы задать вопросы или отправить отзыв, воспользуйтесь одной из возможностей в разделе [Сообщение о проблемах Azure Service Fabric](#report-azure-service-fabric-issues).

## <a name="next-steps"></a>Дальнейшие действия

[Поддерживаемые версии Service Fabric](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform
