---
title: Непрерывная интеграция и развертывание
description: DevOps базы данных корпоративного класса для хранения данных благодаря встроенной поддержке непрерывной интеграции и развертывания с помощью Azure Pipelines.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 131811ffd268f001a047a7031170f0723770d24c
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462332"
---
# <a name="continuous-integration-and-deployment-for-data-warehousing"></a>Непрерывная интеграция и развертывание хранилищ данных

В этом простом учебнике объясняется, как интегрировать проект базы данных SQL Server Data Tools (SSDT) с Azure DevOps и настроить непрерывную интеграцию и развертывание с помощью Azure Pipelines. Это руководство является вторым шагом в создании конвейера непрерывной интеграции и развертывания для хранения данных.

## <a name="before-you-begin"></a>Перед началом

- Изучите [учебник по интеграции системы управления версиями](sql-data-warehouse-source-control-integration.md).

- Настройка и подключение к Azure DevOps

## <a name="continuous-integration-with-visual-studio-build"></a>Непрерывная интеграция с использованием сборки Visual Studio

1. Перейдите к Azure Pipelines и создайте конвейер сборки.

      ![Новый конвейер](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Новый конвейер")

2. Выберите репозиторий исходного кода (Azure Repos Git) и шаблон классического приложения .NET.

      ![Установка конвейера](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Установка конвейера")

3. Измените файл YAML, указав соответствующий пул агента. Файл YAML должен выглядеть примерно так:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

На этом этапе у вас есть простая среда, в которой в результате любого возврата к главной ветви репозитория системы управления версиями автоматически активируется успешная сборка проекта базы данных в Visual Studio. Убедитесь, что автоматизация работает комплексно, внеся изменение в проект локальной базы данных и возвратив это изменение в главную ветвь.

## <a name="continuous-deployment-with-the-azure-synapse-analytics-or-database-deployment-task"></a>Непрерывное развертывание с помощью задачи развертывания Azure синапсе Analytics (или базы данных)

1. Добавьте новую задачу с помощью [задачи развертывания Базы данных SQL Azure](/azure/devops/pipelines/targets/azure-sqldb) и заполните необходимые поля, чтобы подключиться к целевому хранилищу данных. При запуске этой задачи DACPAC, созданный в результате предыдущего процесса сборки, развертывается в целевом хранилище данных. Вы также можете использовать [задачу развертывания Azure синапсе Analytics](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment).

      ![Задача развертывания](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Задача развертывания")

2. Если вы используете собственный агент, убедитесь, что для переменной среды задано использование правильного SqlPackage.exe для Azure синапсе Analytics. Путь должен выглядеть следующим образом:

      ![Переменная среды](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Переменная среды")

   C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Запустите и проверьте свой конвейер. Вы можете вносить изменения локально и возвращать изменения в системе управления версиями, в результате чего должна выполняться автоматическая сборка и развертывание.

## <a name="next-steps"></a>Дальнейшие действия

- Изучение [архитектуры MPP пула SQL синапсе](massively-parallel-processing-mpp-architecture.md)
- Быстрое создание [пула SQL](create-data-warehouse-portal.md)
- [Отправка примера данных](load-data-from-azure-blob-storage-using-polybase.md)
- Просмотрите [видео](sql-data-warehouse-videos.md).
