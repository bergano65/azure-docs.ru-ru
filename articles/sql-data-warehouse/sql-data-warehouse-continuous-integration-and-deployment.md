---
title: Непрерывная интеграция и развертывание
description: Использование DevOps с базой данных корпоративного класса для Хранилища данных SQL со встроенной поддержкой непрерывной интеграции и развертывания с помощью Azure Pipelines.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/28/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: a8178e5ff9ff4816ddd422d3c45cfc0e1e0b3d41
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712983"
---
# <a name="continuous-integration-and-deployment-for-azure-sql-data-warehouse"></a>Непрерывная интеграция и развертывание в Хранилище данных SQL Azure

В этом простом учебнике объясняется, как интегрировать проект базы данных SQL Server Data Tools (SSDT) с Azure DevOps и настроить непрерывную интеграцию и развертывание с помощью Azure Pipelines. Этот учебник является вторым шагом в создании конвейера непрерывной интеграции и развертывания в Хранилище данных SQL. 

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

- Изучите [архитектуру хранилища данных SQL Azure](massively-parallel-processing-mpp-architecture.md).
- Быстро [создайте Хранилище данных SQL Azure](create-data-warehouse-portal.md).
- [Отправка примера данных](sql-data-warehouse-load-sample-databases.md)
- Просмотрите [видео](/azure/sql-data-warehouse/sql-data-warehouse-videos).
