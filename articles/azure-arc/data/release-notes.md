---
title: Службы данных с поддержкой дуги Azure — заметки о выпуске
description: Последние заметки о выпуске
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 5908083be4e6ed389b606754ffef41a4a371c3e3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938728"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Заметки о выпуске — службы данных с поддержкой ARC в Azure (Предварительная версия)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>Сентябрь 2020 г.

Службы данных, включенные в службу Arc Azure, выпущены для общедоступной предварительной версии. Службы данных с поддержкой Arc позволяют управлять службами данных где угодно.

- Управляемый экземпляр SQL
- Гипермасштабирование PostgreSQL.

Инструкции см. в статье [что такое службы данных с поддержкой дуги Azure?](overview.md)

## <a name="next-steps"></a>Следующие шаги

> **Хотите попробовать?**  
> Быстро Начните работу с помощью [Azure Arc JumpStart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) в службе Azure Kubernetes Service (AKS), AWS эластичной Kubernetes Service (ЕКС), Google Cloud Kubernetes Engine (гке) или на виртуальной машине Azure.

[Установка клиентских средств](install-client-tools.md)

[Создание контроллера данных Arc Azure](create-data-controller.md) (требуется сначала установить клиентские средства)

[Создание управляемого экземпляра SQL Azure в службе "Дуга" Azure](create-sql-managed-instance.md) (сначала требуется создание контроллера данных ARC в Azure)

[Создание группы горизонтального масштабирования сервера базы данных Azure для PostgreSQL в службе "Дуга Azure](create-postgresql-hyperscale-server-group.md) " (сначала требуется создание контроллера данных Arc Azure)

## <a name="known-limitations-and-issues"></a>Известные ограничения и проблемы

- Длина имен управляемых экземпляров SQL не может превышать 13 символов
- Нет обновления на месте для контроллера данных ARC в Azure или экземпляров базы данных.
- Образы контейнеров служб данных с поддержкой Arc не подписаны.  Возможно, потребуется настроить узлы Kubernetes, чтобы разрешить отвлекать неподписанные образы контейнера.  Например, если в качестве среды выполнения контейнера используется DOCKER, можно задать переменную среды DOCKER_CONTENT_TRUST = 0 и перезапустить.  Другие среды выполнения контейнеров имеют аналогичные параметры, например в [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Не удается создать на основе портал Azure службы "Дуга Azure", управляемые экземпляры SQL или группы серверов PostgreSQL.
- Пока вы используете NFS, перед созданием контроллера данных ARC в Azure необходимо задать для Алловрунасрут значение true в файле профиля развертывания.
- Только проверка подлинности входа SQL и PostgreSQL.  Отсутствует поддержка Azure Active Directory или Active Directory.
- Создание контроллера данных в OpenShift требует ослабления ограничений безопасности.  Дополнительные сведения см. в документации.
- Масштабирование количества postgres _рабочих узлов рабочей роли не поддерживается_ .
- Если вы используете ядро службы Kubernetes Azure (AKS Engine) в центре Azure Stack с контроллером данных Arc и экземплярами базы данных Azure, обновление до более новой версии Kubernetes не поддерживается. Удалите контроллер данных ARC в Azure и все экземпляры базы данных перед обновлением кластера Kubernetes.
- Предварительный просмотр не поддерживает резервное копирование и восстановление для модуля postgres версии 11. Он поддерживает только резервное копирование и восстановление для postgres версии 12.
