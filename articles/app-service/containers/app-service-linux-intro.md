---
title: Выполнение кода в контейнерах Linux по умолчанию
description: Служба приложений Azure может выполнять код в предварительно созданных контейнерах Linux. Узнайте о возможностях запуска веб-приложений Linux в Azure.
keywords: служба приложений azure, linux, oss
author: msangapu-msft
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 891e0c18b3f95dca905fbc14b957af773135eeec
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/30/2020
ms.locfileid: "85557897"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Вводные сведения о службе приложений Azure на платформе Linux

[Служба приложений Azure](../overview.md) — это полностью управляемая вычислительная платформа, оптимизированная для размещения веб-сайтов и веб-приложений. Служба приложений, предоставляемая на платформе Linux, позволяет клиентам размещать веб-приложения из поддерживаемых стеков приложений изначально в Linux.

## <a name="languages"></a>Языки

Служба приложений на платформе Linux поддерживает ряд встроенных образов для повышения производительности разработчиков. Поддерживаются следующие языки: Node.js, Java (JRE 8 и JRE 11), PHP, Python, .NET Core и Ruby. Выполните [`az webapp list-runtimes --linux`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes), чтобы просмотреть поддерживаемые языки и версии. Если среда выполнения, которая необходима для приложения, не поддерживается во встроенных образах, см. инструкции по [созданию собственного образа Docker](tutorial-custom-docker-image.md) для развертывания в службе "Веб-приложения для контейнеров".

## <a name="deployments"></a>Развернутые приложения

* FTP
* Локальный репозиторий Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Промежуточные среды
* [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro) и непрерывная интеграция и непрерывное развертывание DockerHub.

## <a name="console-publishing-and-debugging"></a>Консоль, публикация и отладка

* Среды
* Развернутые приложения
* базовая консоль;
* SSH

## <a name="scaling"></a>Масштабирование

* Клиенты могут масштабировать веб-приложения, изменяя уровень [плана службы приложений](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json).

## <a name="locations"></a>Расположения

Проверьте [панель мониторинга состояния Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Ограничения

На портале Azure отображаются только те функции, которые уже доступны в веб-приложении для контейнеров. Дополнительные функции будут отображаться на портале по мере добавления.

Служба приложений в Linux поддерживается только в рамках таких планов службы приложений: [Бесплатный, Базовый, Стандартный, Премиум и Изолированный](https://azure.microsoft.com/pricing/details/app-service/plans/). Уровень [Общий](https://azure.microsoft.com/pricing/details/app-service/plans/) недоступен. Вы не можете создать веб-приложение Linux в плане службы приложений, в котором уже размещены веб-приложения для других платформ.  

Текущие ограничения не позволяют комбинировать приложения Windows и Linux в одной группе ресурсов.

## <a name="troubleshooting"></a>Устранение неполадок

> [!NOTE]
> В службе [Мониторинг Azure (предварительная версия)](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#send-logs-to-azure-monitor-preview) есть новые возможности интегрированного ведения журнала. 
>
>

Если не удается запустить приложение или необходимо проверить ведение журнала приложения, просмотрите журналы Docker в каталоге LogFiles. Доступ к этому каталогу можно получить либо на сайте SCM, либо через FTP. Чтобы записать `stdout` и `stderr` из контейнера, необходимо включить **ведение журнала приложения** в разделе **Журналы Службы приложений**. Изменение параметров вступает в силу немедленно. Служба приложений обнаруживает изменение и автоматически перезапускает контейнер.

Получить доступ к сайту SCM можно, щелкнув **Дополнительные средства** в меню **Средства разработки**.

![Просмотр журналов Docker с помощью Kudu][1]

## <a name="next-steps"></a>Дальнейшие действия

Следующие статьи помогут вам приступить к работе со службой приложений на платформе Linux с помощью веб-приложений, написанных на разных языках:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [GO](quickstart-docker-go.md)
* [Создание многоконтейнерного приложения](quickstart-multi-container.md)

Дополнительные сведения о Службе приложений в Linux см. в следующих материалах:

* [Служба приложений Azure на платформе Linux: вопросы и ответы](app-service-linux-faq.md)
* [Поддержка SSH для службы приложений Azure в Linux](app-service-linux-ssh-support.md)
* [Настройка промежуточных сред в службе приложений Azure](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Непрерывное развертывание с использованием платформы Azure "Веб-приложения для контейнеров"](app-service-linux-ci-cd.md)

Если у вас возникли вопросы, опубликуйте их на [нашем форуме](https://docs.microsoft.com/answers/topics/azure-webapps.html).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
