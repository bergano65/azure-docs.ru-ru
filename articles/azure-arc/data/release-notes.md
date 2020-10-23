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
ms.openlocfilehash: 3c20bbd3ab02cd1eccd00e2d36c14eebf2f63205
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92360325"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Заметки о выпуске — службы данных с поддержкой ARC в Azure (Предварительная версия)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>Сентябрь 2020 г.

Службы данных, включенные в службу Arc Azure, выпущены для общедоступной предварительной версии. Службы данных с поддержкой Arc позволяют управлять службами данных где угодно.

- Управляемый экземпляр SQL
- Гипермасштабирование PostgreSQL.

Инструкции см. в статье [что такое службы данных с поддержкой дуги Azure?](overview.md)

### <a name="known-issues"></a>Известные проблемы

В этом выпуске действуют следующие проблемы.

* **Удаление группы серверов PostgreSQL Scale**: Если вы изменили конфигурацию группы серверов или экземпляра, дождитесь завершения операции изменения, прежде чем удалить группу серверов PostgreSQL Scale.

* ** `azdata notebook run` может завершиться ошибкой**: чтобы обойти эту проблему, выполните команду `azdata notebook run` в виртуальной среде Python. Эта проблема также возникает при неудачной попытке создать PostgreSQL группу серверов SQL Server с помощью мастера развертывания Azure Data Studio. В этом случае можно открыть записную книжку и нажать кнопку **запустить все** в верхней части записной книжки.

## <a name="next-steps"></a>Следующие шаги

> **Хотите попробовать?**  
> Быстро приступите к работе с [Azure Arc](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) в службе Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) или на виртуальной машине Azure.

[Установка клиентских средств](install-client-tools.md)

[Создание контроллера данных Azure Arc](create-data-controller.md) (сначала необходимо установить клиентские средства)

[Создание управляемого экземпляра SQL Azure в Azure Arc](create-sql-managed-instance.md) (сначала требуется создать контроллер данных Azure Arc)

[Создание группы серверов с уровнем "Гипермасштабирование" Базы данных Azure для PostgreSQL в Azure Arc ](create-postgresql-hyperscale-server-group.md) (сначала необходимо создать контроллер данных Azure Arc)

## <a name="known-limitations-and-issues"></a>Известные ограничения и проблемы

- Длина имен управляемых экземпляров SQL не может превышать 13 символов
- Нет обновления на месте для контроллера данных ARC в Azure или экземпляров базы данных.
- Образы контейнеров служб данных с поддержкой Arc не подписаны.  Возможно, потребуется настроить узлы Kubernetes так, чтобы разрешить извлечь неподписанные образы контейнера.  Например, если в качестве среды выполнения контейнера используется DOCKER, можно задать переменную среды DOCKER_CONTENT_TRUST = 0 и перезапустить.  Другие среды выполнения контейнеров имеют аналогичные параметры, например в [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Не удается создать на основе портал Azure службы "Дуга Azure", управляемые экземпляры SQL или группы серверов PostgreSQL.
- Пока вы используете NFS, перед созданием контроллера данных ARC в Azure необходимо задать для Алловрунасрут значение true в файле профиля развертывания.
- Только проверка подлинности входа SQL и PostgreSQL.  Отсутствует поддержка Azure Active Directory или Active Directory.
- Создание контроллера данных в OpenShift требует ослабления ограничений безопасности.  Дополнительные сведения см. в документации.
- Масштабирование количества postgres _рабочих узлов рабочей роли не поддерживается_ .
- Если вы используете ядро службы Kubernetes Azure (AKS Engine) в центре Azure Stack с контроллером данных Arc и экземплярами базы данных Azure, обновление до более новой версии Kubernetes не поддерживается. Удалите контроллер данных ARC в Azure и все экземпляры базы данных перед обновлением кластера Kubernetes.
- Предварительный просмотр не поддерживает резервное копирование и восстановление для модуля postgres версии 11. Он поддерживает только резервное копирование и восстановление для postgres версии 12.
- Служба Azure Kubernetes Service (AKS). в настоящее время кластеры, охватывающие [несколько зон доступности](../../aks/availability-zones.md) , не поддерживаются для служб данных с поддержкой службы "Дуга Azure". Чтобы избежать этой проблемы, при создании кластера AKS в портал Azure при выборе региона, в котором доступны зоны, удалите все зоны из элемента управления выбором. См. следующее изображение:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Снимите флажки для каждой зоны, чтобы указать значение нет.":::

  
