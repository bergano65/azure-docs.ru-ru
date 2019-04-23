---
title: Описание вариантов поддержки Azure Service Fabric | Документация Майкрософт
description: Поддерживаемые версии кластера Service Fabric и ссылки для отправки запросов в службу поддержки.
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: a931de8be07d41cf4daab63aa7691973ee158452
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005049"
---
# <a name="azure-service-fabric-support-options"></a>Варианты поддержки Azure Service Fabric

Чтобы обеспечить надлежащую поддержку кластеров Service Fabric, выполняющих рабочие нагрузки ваших приложений, мы подготовили для вас различные варианты. Вы можете выбрать соответствующие варианты в зависимости от нужного уровня поддержки и серьезности проблемы. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Сообщение о проблемах рабочей среды или запрос платной поддержки для Azure

Чтобы сообщить о проблемах в кластере Service Fabric, развернутом в Azure, откройте запрос в службу поддержки [на портале Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) или [портале службы поддержки Майкрософт](https://support.microsoft.com/oas/default.aspx?prid=16146).

См. также:
 
- [Поддержка корпорации Майкрософт для клиентов Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Поддержка Premier](https://support.microsoft.com/en-us/premier).

> [!Note]
> Кластеры, работающие на уровне надежности бронзовую дают возможность запуска только тестов для рабочих нагрузок. При возникновении проблем с кластера под управлением бронзового надежности, служба поддержки Майкрософт поможет вам в устранение проблемы, но не будет выполнять анализ основных причин. Обратитесь к [характеристики надежности кластера](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) для получения дополнительных сведений.
>
> Дополнительные сведения о том, что требуется для рабочего кластера все готово, обратитесь к [контрольный список готовности рабочей](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Сообщение о проблемах рабочей среды или запрос платной поддержки для изолированных кластеров Service Fabric

Чтобы сообщить о проблемах в кластере Service Fabric, развернутом в локальной среде или облаке стороннего производителя, откройте запрос в профессиональную службу поддержки на [портале службы поддержки Майкрософт](https://support.microsoft.com/oas/default.aspx?prid=16146).

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

[Форум отзывов и предложений по Azure по Service Fabric][uservoice-forum] — лучший форум, на котором можно поделиться идеями о важных функциях для продукта, так как мы рассматриваем наиболее популярные запросы в процессе среднесрочного и долгосрочного планирования. Мы рекомендуем вам обращаться с предложениями в службу поддержки в этом сообществе.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Предварительные версии Service Fabric не поддерживают работу в рабочей среде.

Время от времени мы выпускаем версии с важными функциями, доступными в виде предварительных версий, отзывы по которым мы хотим получить. Эти предварительные версии следует использовать только для целей тестирования. На производственном кластере всегда должна быть запущена поддерживаемая стабильная версия Service Fabric. Предварительная версия всегда начинается с номера основной и вспомогательной версии, равного 255. Например, если отображается версия Service Fabric 255.255.5703.949, она является предварительной и ее следует использовать в тестовых кластерах. О выходе этих предварительных выпусков также объявляется в [блоге группы Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) и приводятся сведения о функциях, включенных в их состав.
Возможность платной технической поддержки для этих предварительных выпусков отсутствует. Чтобы задать вопросы или отправить отзыв, воспользуйтесь одной из возможностей в разделе [Сообщение о проблемах Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues).

## <a name="next-steps"></a>Дальнейшие действия

[Поддерживаемые версии Service Fabric](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples