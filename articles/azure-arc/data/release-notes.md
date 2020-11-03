---
title: Службы данных с поддержкой дуги Azure — заметки о выпуске
description: Последние заметки о выпуске
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 10/29/2020
ms.topic: conceptual
ms.openlocfilehash: e7312ffd4d55f0403359f8aad2d0a8433a716f77
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280376"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Заметки о выпуске — службы данных с поддержкой ARC в Azure (Предварительная версия)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="october-2020"></a>Октябрь 2020 г. 

### <a name="breaking-changes"></a>Критические изменения

В этом выпуске представлены следующие критические изменения. 

* Файлы пользовательского определения ресурсов PostgreSQL (CRD) заменяют термин `shards` на `workers` . Этот термин ( `workers` ) соответствует имени параметра командной строки.

* `azdata arc postgres server delete` запрашивает подтверждение перед удалением экземпляра postgres.  Используйте `--force` , чтобы пропустить запрос.

### <a name="additional-changes"></a>Дополнительные изменения

* К вызову был добавлен новый необязательный параметр `azdata arc postgres server create` `--volume-claim mounts` . Значение представляет собой разделенный запятыми список подключений к подключениям к тому. Подключение с утверждением тома — это пара типов томов и ПОСТОЯННЫХ имен. Тип тома теперь допускает только `backup` .  В PostgreSQL, если тип тома — `backup` , PVC монтируется в `/mnt/db-backups` .  Это обеспечивает общий доступ к резервным копиям между экземплярами postgres, чтобы можно было восстановить резервную копию одного экземпляра postgres в другом.

* Новые короткие имена для пользовательских определений ресурсов PostgresSQL: 

  * `pg11` 

  * `pg12`

* Передача данных телеметрии предоставляет пользователю одно из следующих действий:

   * Количество точек, отправленных в Azure

     или 

   * Если в Azure не были загружены данные, выводится приглашение повторить попытку.

* `azdata arc dc debug copy-logs` Теперь также считывает из `/var/opt/controller/log` папки и собирает журналы postgres.

*   Отображать рабочий индикатор во время postgres создания и восстановления резервной копии.

* `azdata arc postrgres backup list` теперь содержит сведения о размере резервной копии.

* Свойство SQL Управляемый экземпляр Admin Name добавлено в столбец "правый" в колонке "Обзор" в портал Azure.



## <a name="september-2020"></a>Сентябрь 2020 г.

Службы данных, включенные в службу Arc Azure, выпущены для общедоступной предварительной версии. Службы данных с поддержкой Arc позволяют управлять службами данных где угодно.

- Управляемый экземпляр SQL
- Гипермасштабирование PostgreSQL.

Инструкции см. в статье [что такое службы данных с поддержкой дуги Azure?](overview.md)

## <a name="known-limitations-and-issues"></a>Известные ограничения и проблемы

- Длина имен управляемых экземпляров SQL не может превышать 13 символов
- Нет обновления на месте для контроллера данных ARC в Azure или экземпляров базы данных.
- Образы контейнеров служб данных с поддержкой Arc не подписаны.  Возможно, потребуется настроить узлы Kubernetes так, чтобы разрешить извлечь неподписанные образы контейнера.  Например, если в качестве среды выполнения контейнера используется DOCKER, можно задать переменную среды DOCKER_CONTENT_TRUST = 0 и перезапустить.  Другие среды выполнения контейнеров имеют аналогичные параметры, например в [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Не удается создать на основе портал Azure службы "Дуга Azure", управляемые экземпляры SQL или группы серверов PostgreSQL.
- Пока вы используете NFS, `allowRunAsRoot` `true` перед созданием контроллера данных ARC в Azure необходимо задать в файле профиля развертывания.
- Только проверка подлинности входа SQL и PostgreSQL.  Отсутствует поддержка Azure Active Directory или Active Directory.
- Создание контроллера данных в OpenShift требует ослабления ограничений безопасности.  Дополнительные сведения см. в документации.
- Масштабирование количества PostgresSQL _рабочих узлов рабочей роли не поддерживается_ .
- Если вы используете ядро службы Kubernetes Azure (AKS Engine) в центре Azure Stack с контроллером данных Arc и экземплярами базы данных Azure, обновление до более новой версии Kubernetes не поддерживается. Удалите контроллер данных ARC в Azure и все экземпляры базы данных перед обновлением кластера Kubernetes.
- Предварительный просмотр не поддерживает резервное копирование и восстановление для модуля postgres версии 11. Он поддерживает только резервное копирование и восстановление для postgres версии 12.
- Служба Azure Kubernetes Service (AKS). в настоящее время кластеры, охватывающие [несколько зон доступности](../../aks/availability-zones.md) , не поддерживаются для служб данных с поддержкой службы "Дуга Azure". Чтобы избежать этой проблемы, при создании кластера AKS в портал Azure при выборе региона, в котором доступны зоны, удалите все зоны из элемента управления выбором. См. следующее изображение:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Снимите флажки для каждой зоны, чтобы указать значение нет.":::

## <a name="next-steps"></a>Следующие шаги
  
> **Хотите попробовать?**  
> Быстро приступите к работе с [Azure Arc](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) в службе Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) или на виртуальной машине Azure.

[Установка клиентских средств](install-client-tools.md)

[Создание контроллера данных Azure Arc](create-data-controller.md) (сначала необходимо установить клиентские средства)

[Создание управляемого экземпляра SQL Azure в Azure Arc](create-sql-managed-instance.md) (сначала требуется создать контроллер данных Azure Arc)

[Создание группы серверов с уровнем "Гипермасштабирование" Базы данных Azure для PostgreSQL в Azure Arc ](create-postgresql-hyperscale-server-group.md) (сначала необходимо создать контроллер данных Azure Arc)
