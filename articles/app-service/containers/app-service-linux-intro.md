---
title: Вводные сведения о службе приложений на платформе Linux | Документация Майкрософт
description: Информация о службе приложений Azure на платформе Linux.
keywords: служба приложений azure, linux, oss
services: app-service
documentationcenter: ''
author: naziml
manager: cfowler
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/09/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 75eb260b107df3877219266794e45eb11d5deb35
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633246"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Вводные сведения о службе приложений Azure на платформе Linux

[Веб-приложение](../app-service-web-overview.md) — это полностью управляемая вычислительная платформа, оптимизированная для размещения веб-сайтов и веб-приложений. Служба приложений, предоставляемая на платформе Linux, позволяет клиентам размещать веб-приложения из поддерживаемых стеков приложений изначально в Linux. В следующем разделе перечислены поддерживаемые в настоящее время стеки приложений.

## <a name="languages"></a>Языки

Служба приложений на платформе Linux поддерживает ряд встроенных образов для повышения производительности разработчиков. Если среда выполнения, которая необходима для приложения, не поддерживается во встроенных образах, см. инструкции по [созданию собственного образа Docker](tutorial-custom-docker-image.md) для развертывания в службе "Веб-приложения для контейнеров".

| Язык | Поддерживаемые версии |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 9.4, 10.1,10.10 |
| Java * | Tomcat 8.5, 9.0, Java SE, WildFly 14 (все версии, работающие в JRE 8) |
| PHP | 5.6, 7.0, 7.2 |
| Python (предварительная версия) | 3.6, 3.7 |
| .NET Core | 1.0, 1.1, 2.0, 2.1 |
| Ruby | 2.3 |

Дополнительные сведения см. в статье о [создании веб-приложения Java в службе приложений Azure на платформе Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-java).

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

* Клиенты могут масштабировать веб-приложения, изменяя уровень [плана службы приложений](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json).

## <a name="locations"></a>Расположения

Проверьте [панель мониторинга состояния Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Ограничения

На портале Azure отображаются только те функции, которые уже доступны в веб-приложении для контейнеров. Дополнительные функции будут отображаться на портале по мере добавления.

Некоторые функции, такие как интеграция виртуальной сети, аутентификация Azure Active Directory, сторонняя аутентификация или расширения сайтов Kudu, в настоящее время недоступны. После того, как эти функции станут доступны, мы обновим нашу документацию и блог, добавив информацию об изменениях.

Служба приложений на платформе Linux поддерживается только в рамках планов службы приложений ["Базовый", "Стандартный" и "Премиум"](https://azure.microsoft.com/pricing/details/app-service/plans/). Уровни ["Бесплатный" и "Общий"](https://azure.microsoft.com/pricing/details/app-service/plans/) недоступны. Вы не можете создавать веб-приложения для контейнеров в рамках плана службы приложений, в котором уже размещены веб-приложения под управлением платформ, отличных от Linux. Кроме того, сейчас недопустимо комбинировать приложения Windows и Linux в одной и той же группе ресурсов.

## <a name="troubleshooting"></a>Устранение неполадок

Если не удается запустить приложение или необходимо проверить ведение журнала приложения, просмотрите журналы Docker в каталоге LogFiles. Доступ к этому каталогу можно получить либо на сайте SCM, либо через FTP.
Чтобы записать `stdout` и `stderr` из контейнера, необходимо включить **ведение журнала контейнера Docker** в разделе **Журналы диагностики**.

![Включение ведения журнала][2]

![Просмотр журналов Docker с помощью Kudu][1]

Получить доступ к сайту SCM можно, щелкнув **Дополнительные средства** в меню **Средства разработки**.

## <a name="next-steps"></a>Дополнительная информация

Следующие статьи помогут вам приступить к работе со службой приложений на платформе Linux с помощью веб-приложений, написанных на разных языках:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](quickstart-dotnetcore.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [GO](quickstart-docker-go.md)
* [Создание многоконтейнерного приложения](quickstart-multi-container.md)

Также ознакомьтесь со следующими статьями о службе приложений в Linux:

* [Служба приложений Azure на платформе Linux: вопросы и ответы](app-service-linux-faq.md)
* [Поддержка SSH для службы приложений Azure в Linux](app-service-linux-ssh-support.md)
* [Настройка промежуточных сред в службе приложений Azure](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Непрерывное развертывание с использованием платформы Azure "Веб-приложения для контейнеров"](app-service-linux-ci-cd.md)

Если у вас возникли вопросы, опубликуйте их на [нашем форуме](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
