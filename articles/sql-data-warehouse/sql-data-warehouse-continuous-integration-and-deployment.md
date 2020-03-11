---
title: Непрерывная интеграция и развертывание
description: Средство корпоративного класса для хранения данных Database DevOps со встроенной поддержкой непрерывной интеграции и развертывания с помощью Azure Pipelines.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: cb28afc84c423c5c5321a8f948e82f82031888a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195946"
---
# <a name="continuous-integration-and-deployment-for-data-warehousing"></a>Непрерывная интеграция и развертывание для хранилищ данных

В этом простом учебнике объясняется, как интегрировать проект базы данных SQL Server Data Tools (SSDT) с Azure DevOps и настроить непрерывную интеграцию и развертывание с помощью Azure Pipelines. Этот учебник является вторым шагом в создании конвейера непрерывной интеграции и развертывания для хранилищ данных. 

## <a name="before-you-begin"></a>Перед началом

- Изучите [учебник по интеграции системы управления версиями](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration).

- Настройка и подключение к Azure DevOps


## <a name="continuous-integration-with-visual-studio-build"></a>Непрерывная интеграция с использованием сборки Visual Studio

1. Перейдите к Azure Pipelines и создайте конвейер сборки.

      ![Новый конвейер](media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Новый конвейер")

2. Выберите репозиторий исходного кода (Azure Repos Git) и шаблон классического приложения .NET.

      ![Установка конвейера](media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Установка конвейера") 

3. Измените файл YAML, указав соответствующий пул агента. Файл YAML должен выглядеть примерно так:

      ![YAML](media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

На этом этапе у вас есть простая среда, в которой в результате любого возврата к главной ветви репозитория системы управления версиями автоматически активируется успешная сборка проекта базы данных в Visual Studio. Убедитесь, что автоматизация работает комплексно, внеся изменение в проект локальной базы данных и возвратив это изменение в главную ветвь.


## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Непрерывное развертывание с помощью задачи развертывания Data Warehouse (или Базы данных) SQL Azure

1. Добавьте новую задачу с помощью [задачи развертывания Базы данных SQL Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops) и заполните необходимые поля, чтобы подключиться к целевому хранилищу данных. При запуске этой задачи DACPAC, созданный в результате предыдущего процесса сборки, развертывается в целевом хранилище данных. Вы также можете использовать [задачу развертывания Хранилища данных SQL Azure](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment). 

      ![Задача развертывания](media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Задача развертывания")

2. При использовании локального агента убедитесь, что для переменной среды задано использование правильного файла SqlPackage.exe для Хранилища данных SQL. Путь должен выглядеть следующим образом:

      ![Переменная среды](media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Переменная среды")

   C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Запустите и проверьте свой конвейер. Вы можете вносить изменения локально и возвращать изменения в системе управления версиями, в результате чего должна выполняться автоматическая сборка и развертывание.

## <a name="next-steps"></a>Дальнейшие действия

- Изучите [архитектуру MPP для SQL Analytics](massively-parallel-processing-mpp-architecture.md)
- Быстрое создание [пула SQL](create-data-warehouse-portal.md)
- [Отправка примера данных](sql-data-warehouse-load-sample-databases.md)
- Просмотрите [видео](/azure/sql-data-warehouse/sql-data-warehouse-videos).
