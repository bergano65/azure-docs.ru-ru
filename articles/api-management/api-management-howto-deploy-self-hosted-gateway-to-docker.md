---
title: Развертывание самохотвожитых шлюза управления API Azure в Docker (ru) Документы Майкрософт
description: Узнайте, как развернуть самохлаженный шлюз управления API Azure для Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: b5acda30f7f4a01e1b7b6ac82652452c3c40e3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768509"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Развертывание самохлаженного шлюза Управления API Azure в Docker

В этой статье приводятся шаги по развертыванию самостоятельно размещенных шлюзов Управления API Azure в среде Docker.

> [!NOTE]
> Функция самостоятельного входной шлюза находится в предварительном просмотре. Во время предварительного просмотра самоходной шлюз доступен только в уровнях разработчика и Premium без дополнительной платы. Уровень разработчика ограничен одним самоходном развертыванием шлюза.

## <a name="prerequisites"></a>Предварительные требования

- Завершите следующий быстрый запуск: [Создайте экземпляр управления API Azure](get-started-create-service-instance.md)
- Создайте среду Docker. [Docker for Desktop](https://www.docker.com/products/docker-desktop) является хорошим вариантом для целей разработки и оценки. См [документации Docker](https://docs.docker.com) для получения информации обо всех изданиях Docker, их особенностях и всеобъемлющей документации о самом Docker.
- [Предоставление ресурса шлюза в экземпляре Управления API](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Самоуправляемый шлюз упакован как контейнер docker на основе Linux x86-64.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Развертывание самохлаженного шлюза в Docker

1. Выберите **шлюзы** из-под **настроек.**
2. Выберите ресурс шлюза, который вы собираетесь развернуть.
3. Выберите **развертывание.**
4. Обратите внимание, что новый маркер в текстовом поле **Token** был автоматически генерирован для вас с помощью **значений истечения срока действия** по умолчанию и **секретных ключей.** Отрегулируйте один или оба при желании и выберите **Generate** для создания нового маркера.
4. Убедитесь, что **Docker** выбран в соответствии со **скриптами развертывания.**
5. Выберите ссылку файла **env.conf** рядом с **окружающей средой,** чтобы загрузить файл.
6. Выберите значок **копии,** расположенный в правом конце текстового окна **Run,** чтобы сохранить команду Docker в буфер обмена.
7. Вставьте команду в окно терминала (или команды). По мере необходимости отрегулируйте картографирование портов и название контейнера. Обратите внимание, что команда ожидает, что загруженный файл среды будет присутствовать в текущем каталоге.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Выполните команду. Команда инструктирует среду Docker для запуска контейнера, используя самоходном шлюзе, загруженное из реестра контейнеров Майкрософт, и для картирования портов контейнера HTTP (8080) и HTTPS (8081) в порты 80 и 443 на хосте.
9. Выполнить ниже команды, чтобы проверить шлюз стручка работает:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Вернитесь на портал Azure и подтвердите, что только что развернутый узел шлюза сообщает о здоровом состоянии.

![Статус шлюза](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> Используйте <code>console docker container logs <gateway-name></code> команду для просмотра снимка самоходном журнала шлюзов.
>
> Используйте <code>docker container logs --help</code> команду, чтобы просмотреть все параметры просмотра журнала.

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать больше о самохлаженном шлюзе, смотрите [обзор самоуправляемых шлюзов Azure API Management.](self-hosted-gateway-overview.md)
* [Настройка пользовательского доменного имени для самостоятельного шлюза.](api-management-howto-configure-custom-domain-gateway.md)
