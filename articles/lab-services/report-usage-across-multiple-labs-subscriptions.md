---
title: Использование отчетов Azure DevTest Labs в нескольких лабораториях и подписках в Azure | Документация Майкрософт
description: Узнайте, как сообщать об использовании Azure DevTest Labs в нескольких лабораториях и подписках.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: takamath
ms.openlocfilehash: 0d930263233056d8fa74ffe6ccb176ee39429121
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829233"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Использование отчетов Azure DevTest Labs в нескольких лабораториях и подписках

Большинству больших организаций требуется отслеживать использование ресурсов, чтобы более эффективно использовать эти ресурсы, визуализируя тенденции и выбросы в использовании. Основываясь на использовании ресурсов, владельцы и руководители лаборатории могут настраивать лабораторию для [улучшения использования ресурсов и затрат](https://docs.microsoft.com/azure/billing/billing-getting-started). В Azure DevTest Labs можно загрузить использование ресурсов в лабораторию, что позволит более подробно изучить закономерности использования. Эти шаблоны использования могут помочь точно определить изменения для повышения эффективности. Большинству предприятий требуется как отдельное использование лаборатории, так и общее использование в [нескольких лабораториях и подписках](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/). 

В этой статье описывается, как управлять сведениями об использовании ресурсов в нескольких лабораториях и подписках.

![Отчеты об использовании](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Использование отдельных лабораторий

В этом разделе описывается экспорт использования ресурсов для одной лаборатории.

Перед экспортом использования ресурсов DevTest Labs необходимо настроить учетную запись хранения Azure, чтобы разрешить сохранение различных файлов, содержащих данные об использовании. Существует два стандартных способа выполнения экспорта данных.

* [DevTest Labs REST API](https://docs.microsoft.com/rest/api/dtl/labs/exportresourceusage) 
* В PowerShell AZ. Resource Module [вызывается командлет-азресаурцеактион](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2) с действием `exportResourceUsage`, идентификатором ресурса лаборатории и необходимыми параметрами. 

    Статья [Экспорт или удаление персональных данных](personal-data-delete-export.md) содержит пример скрипта PowerShell с подробными сведениями о экспортируемых данных. 

    > [!NOTE]
    > Параметр Date не включает метку времени, поэтому данные включают все, начиная с полуночи, в зависимости от часового пояса, в котором находится лаборатория.

После завершения экспорта в хранилище BLOB-объектов будет несколько CSV-файлов с различными сведениями о ресурсах.
  
В настоящее время существует два CSV-файла:

* *virtualmachines. csv* — содержит сведения о виртуальных машинах в лаборатории.
* *Disks. csv* — содержит сведения о различных дисках в лаборатории. 

Эти файлы хранятся в контейнере больших двоичных объектов *лабресаурцеусаже* под именем лаборатории, в поле "уникальный идентификатор лаборатории", "Дата выполнения" и "полная" или "Дата начала", которая была основана на запросе на экспорт. Пример структуры больших двоичных объектов:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Экспорт использования для всех лабораторий

Чтобы экспортировать сведения об использовании для нескольких лабораторных занятий, используйте 

* [Функции Azure](https://docs.microsoft.com/azure/azure-functions/), доступные на многих языках, включая PowerShell, или 
* [Runbook службы автоматизации Azure](https://docs.microsoft.com/azure/automation/), используйте PowerShell, Python или пользовательский графический конструктор для написания кода экспорта.

Используя эти технологии, можно выполнять отдельные экспорты лаборатории для всех лабораторий в определенные даты и время. 

Функция Azure должна отправлять данные в долгосрочное хранение. Экспорт данных для нескольких лабораторных занятий может занять некоторое время. Для повышения производительности и снижения вероятности дублирования информации рекомендуется выполнять каждую лабораторию параллельно. Для достижения параллелизма запустите функции Azure асинхронно. Также воспользуйтесь триггером таймера, предлагаемым функциями Azure.

## <a name="using-a-long-term-storage"></a>Использование долгосрочного хранения

Долгосрочное хранение объединяет данные экспорта из разных лабораторий в один источник данных. Еще одним преимуществом использования долгосрочного хранения является возможность удалить файлы из учетной записи хранения, чтобы снизить дублирование и стоимость. 

Долгосрочное хранение можно использовать для обработки текста, например: 

* Добавление понятных имен
* Создание сложных группирований
* Статистическая обработка данных.

Ниже приведены некоторые распространенные решения для хранения данных. [SQL Server](https://azure.microsoft.com/services/sql-database/), [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)и [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Выбор выбранного решения долгосрочного хранения зависит от предпочтений. Вы можете выбрать средство в зависимости от того, что оно предлагает в плане доступности взаимодействия при визуализации данных.

## <a name="visualizing-data-and-gathering-insights"></a>Визуализация данных и сбор ценных сведений

Используйте выбранное средство визуализации данных, чтобы подключиться к долгосрочному хранилищу для отображения данных об использовании и сбора ценных сведений для проверки эффективности использования. Например, [Power BI](https://docs.microsoft.com/power-bi/power-bi-overview) можно использовать для упорядочения и вывода данных об использовании. 

[Фабрику данных Azure](https://azure.microsoft.com/services/data-factory/) можно использовать для создания, связывания и управления ресурсами в одном интерфейсе размещения. Если требуется больший контроль, отдельный ресурс можно создать в одной группе ресурсов и управлять им независимо от службы фабрики данных.  

## <a name="next-steps"></a>Следующие шаги

Когда система будет настроена и данные переходят в долгосрочное хранение, на следующем этапе следует указать вопросы, на которые должны ответить эти данные. Пример: 

-   Каково использование размера виртуальной машины?

    Выбирают ли пользователи высокопроизводительные (более ресурсоемкие) размеры виртуальных машин?
-   Какие образы Marketplace используются?

    Являются ли пользовательские образы наиболее распространенной базой виртуальных машин, если хранилище общих образов строится как [Коллекция общих образов](../virtual-machines/windows/shared-image-galleries.md) или [фабрика образов](image-factory-create.md).
-   Какие пользовательские образы используются или не используются?
