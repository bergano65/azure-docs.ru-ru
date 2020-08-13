---
title: Руководство по аварийному восстановлению Azure Data Lake Storage 1-го поколения | Документы Майкрософт
description: Узнайте, как обеспечить дополнительную защиту данных от небольшого количества сбоев или случайных удалений за пределами локально избыточного хранилища Azure Data Lake Storage 1-го поколения.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: b124f828c4a6a019c45243528ed2d957e3f781f3
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88191415"
---
# <a name="high-availability-and-disaster-recovery-guidance-for-data-lake-storage-gen1"></a>Рекомендации по обеспечению высокого уровня доступности и аварийного восстановления для Data Lake Storage 1-го поколения

Data Lake Storage 1-го поколения предоставляет локально избыточное хранилище (LRS). Таким образом, данные в учетной записи Data Lake Storage 1-го поколения устойчивы к временным сбоям оборудования в центре обработки данных через автоматические реплики. Это гарантирует устойчивость и высокий уровень доступности данных, а также соответствие требованиям соглашения об уровне обслуживания Data Lake Storage 1-го поколения. В этой статье приведены рекомендации по защите данных в случае редких сбоев оборудования в регионе или случайного удаления.

## <a name="disaster-recovery-guidance"></a>Руководство по аварийному восстановлению

Для подготовки плана аварийного восстановления крайне важно. Ознакомьтесь с информацией в этой статье и дополнительными ресурсами, которые помогут вам создать собственный план.

* [Аварийное восстановление и высокий уровень доступности для приложений Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Техническое руководство по обеспечению устойчивости в Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practice-recommendations"></a>Рекомендации

Мы рекомендуем копировать важные данные в другую учетную запись Data Lake Storage 1-го поколения, расположенную в другом регионе. Выполняйте эту процедуру так часто, как того требует ваш план аварийного восстановления. Существует целый ряд методов копирования данных, включая [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md)или [фабрику данных Azure](../data-factory/connector-azure-data-lake-store.md). Фабрика данных Azure — это полезная служба для создания и развертывания конвейеров перемещения данных на регулярной основе.

В случае регионального сбоя можно получить доступ к данным в регионе, куда были скопированы данные. Чтобы определить состояние службы Azure в разных регионах мира, можно использовать [панель мониторинга работоспособности службы Azure](https://azure.microsoft.com/status/).

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Руководство по восстановлению данных после повреждения или случайного удаления

Несмотря на то, что Data Lake Storage 1-го поколения обеспечивает устойчивость данных за счет автоматических реплик, они по-прежнему не защищены от повреждения или случайного удаления в приложении (а также разработчиками или пользователями).

Чтобы предотвратить случайное удаление данных, для начала мы рекомендуем настроить для своей учетной записи Data Lake Storage 1-го поколения правильные политики доступа, Сюда входит применение [блокировок ресурсов Azure](../azure-resource-manager/management/lock-resources.md) для блокировки важных ресурсов и управления доступом на уровне учетных записей и файлов с помощью доступных [функций безопасности Data Lake Storage 1-го поколения](data-lake-store-security-overview.md). Кроме того, мы советуем регулярно создавать копии важных данных в других учетных записях Data Lake Storage 1-го поколения, папках и подписках Azure с помощью [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) или [фабрики данных Azure](../data-factory/connector-azure-data-lake-store.md). Таким образом вы сможете восстановить данные после повреждения или случайного удаления. Фабрика данных Azure — это полезная служба для создания и развертывания конвейеров перемещения данных на регулярной основе.

Кроме того, можно включить [ведение журнала диагностики](data-lake-store-diagnostic-logs.md) для учетной записи Data Lake Storage 1-го поколения для сбора журналов аудита доступа к данным. Журнал аудита содержит сведения о том, кто мог удалить или обновить файл.

Вы можете попытаться восстановить удаленный элемент с помощью модуля [AZ. data lake store](https://docs.microsoft.com/powershell/module/az.datalakestore/) Azure PowerShell для Data Lake Storage Gen 1. В частности, см. команду [RESTORE-аздаталакестоределетедитем](https://docs.microsoft.com/powershell/module/az.datalakestore/restore-azdatalakestoredeleteditem) . Перед использованием этой команды обязательно ознакомьтесь с [описанием](https://docs.microsoft.com/powershell/module/az.datalakestore/restore-azdatalakestoredeleteditem#description) раздела.

## <a name="next-steps"></a>Дальнейшие шаги

* [Начало работы с Data Lake Storage 1-го поколения](data-lake-store-get-started-portal.md)
* [Защита данных в Data Lake Storage Gen1](data-lake-store-secure-data.md)
