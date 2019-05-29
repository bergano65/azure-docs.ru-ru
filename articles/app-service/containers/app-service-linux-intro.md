---
title: Знакомство со Служба приложений Azure на платформе Linux | Документация Майкрософт
description: Информация о службе приложений Azure на платформе Linux.
keywords: служба приложений azure, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu;yili
ms.custom: seodec18
ms.openlocfilehash: 8d40b28103e24214ef00864b032266cc95889eff
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780334"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Вводные сведения о службе приложений Azure на платформе Linux

[Служба приложений Azure](../overview.md) — это полностью управляемая вычислительная платформа, оптимизированная для размещения веб-сайтов и веб-приложений. Служба приложений, предоставляемая на платформе Linux, позволяет клиентам размещать веб-приложения из поддерживаемых стеков приложений изначально в Linux. В разделе [Языки](#languages) перечислены поддерживаемые в настоящее время стеки приложений.

## <a name="languages"></a>Языки

Служба приложений на платформе Linux поддерживает ряд встроенных образов для повышения производительности разработчиков. Если среда выполнения, которая необходима для приложения, не поддерживается во встроенных образах, см. инструкции по [созданию собственного образа Docker](tutorial-custom-docker-image.md) для развертывания в службе "Веб-приложения для контейнеров".

| Язык | Поддерживаемые версии |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 9.4, 10.1,10.10, 10.14 |
| Java * | Tomcat 8.5, 9.0, Java SE, WildFly 14 (все версии, работающие в JRE 8) |
| PHP | 5.6, 7.0, 7.2 |
| Python | 2.7, 3.6, 3.7 |
| .NET Core | 1.0, 1.1, 2.0, 2.1, 2.2 |
| Ruby | 2.3, 2.4 |

## <a name="deployments"></a>Развернутые приложения

* FTP
* локальный репозиторий Git;
* GitHub
* Bitbucket;

## <a name="devops"></a>DevOps

* Промежуточные среды
* [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro) и непрерывная интеграция и непрерывное развертывание DockerHub.

## <a name="console-publishing-and-debugging"></a>Консоль, публикация и отладка

* средами;
* Развернутые приложения
* базовая консоль;
* SSH

## <a name="scaling"></a>Масштабирование

* Клиенты могут масштабировать веб-приложения, изменяя уровень [плана службы приложений](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json).

## <a name="locations"></a>Расположения

Проверьте [панель мониторинга состояния Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Ограничения

На портале Azure отображаются только те функции, которые уже доступны в веб-приложении для контейнеров. Дополнительные функции будут отображаться на портале по мере добавления.

Служба приложений в Linux поддерживается только в рамках планов службы приложений ["Бесплатный", "Базовый", "Стандартный" и "Премиум"](https://azure.microsoft.com/pricing/details/app-service/plans/). Уровень ["Общий"](https://azure.microsoft.com/pricing/details/app-service/plans/) недоступен. Вы не можете создать веб-приложение Linux в плане службы приложений, в котором уже размещены веб-приложения для других платформ.  

Текущие ограничения не позволяют комбинировать приложения Windows и Linux в одной группе ресурсов.

## <a name="troubleshooting"></a>Устранение неполадок

Если не удается запустить приложение или необходимо проверить ведение журнала приложения, просмотрите журналы Docker в каталоге LogFiles. Доступ к этому каталогу можно получить либо на сайте SCM, либо через FTP.
Чтобы записать `stdout` и `stderr` из контейнера, необходимо включить **ведение журнала контейнера Docker** в разделе **Журналы диагностики**.

![Включение ведения журнала][2]

Изменение параметров вступает в силу немедленно. Служба приложений обнаруживает изменение параметров и автоматически перезапускает контейнер.

Получить доступ к сайту SCM можно, щелкнув **Дополнительные средства** в меню **Средства разработки**.

![Просмотр журналов Docker с помощью Kudu][1]

## <a name="next-steps"></a>Дополнительная информация

Следующие статьи помогут вам приступить к работе со службой приложений на платформе Linux с помощью веб-приложений, написанных на разных языках:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [Создание многоконтейнерного приложения](quickstart-multi-container.md)

Дополнительные сведения о Службе приложений в Linux см. в следующих материалах:

* [Служба приложений Azure на платформе Linux: вопросы и ответы](app-service-linux-faq.md)
* [Поддержка SSH для службы приложений Azure в Linux](app-service-linux-ssh-support.md)
* [Настройка промежуточных сред в службе приложений Azure](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Непрерывное развертывание с использованием платформы Azure "Веб-приложения для контейнеров"](app-service-linux-ci-cd.md)

Если у вас возникли вопросы, опубликуйте их на [нашем форуме](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
