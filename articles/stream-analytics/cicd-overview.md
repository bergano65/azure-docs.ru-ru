---
title: Непрерывная интеграция и развертывание для Azure Stream Analytics
description: В этой статье приводятся общие сведения о конвейере непрерывной интеграции и развертывания (CI/CD) для Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: ec8f27d0376f7187fd36b3feba556dbced0946e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937595"
---
# <a name="continuous-integration-and-deployment-cicd-for-azure-stream-analytics"></a>Непрерывная интеграция и развертывание (CI/CD) для Azure Stream Analytics

Вы можете непрерывно развернуть задание Azure Stream Analytics с помощью интеграции системы управления версиями. Интеграция системы управления версиями позволяет рабочему процессу, в котором обновление кода активирует развертывание ресурсов в Azure. В этой статье описаны основные шаги по созданию конвейера непрерывной интеграции и развертывания (CI/CD).

Если вы не знакомы с Azure Stream Analytics, начните работу с [кратким](stream-analytics-quick-create-portal.md)руководством по Azure Stream Analytics.

## <a name="create-a-cicd-pipeline"></a>Создание конвейера CI/CD

Выполните действия, описанные в этом руководстве, чтобы создать конвейер CI/CD для Stream Analytics.

1. Разработка Azure Stream Analytics запроса.

   Используйте средства Azure Stream Analytics для [Visual Studio Code](quick-create-vs-code.md) или [Visual Studio](stream-analytics-quick-create-vs.md) , чтобы [локально разрабатывать и тестировать запросы](develop-locally.md). Вы также можете [экспортировать существующее задание](visual-studio-code-explore-jobs.md#export-a-job-to-a-local-project) в локальный проект.

2. Зафиксируйте Azure Stream Analytics проекты в системе управления версиями, например в репозитории Git.

3. Используйте [Azure Stream Analytics средства CI/CD](cicd-tools.md) для создания проектов и создания шаблонов управления ресурсами Azure для развертывания.

4. Выполнение [тестов автоматического выполнения сценариев](cicd-tools.md#automated-test) для оценки качества.

5. Автоматическое [Развертывание задания](cicd-tools.md#deploy-to-azure) в Azure.

## <a name="auto-build-test-and-deploy"></a>Автоматическая сборка, тестирование и развертывание

Для автоматической сборки, тестирования и развертывания можно использовать командную строку и [средства Azure Stream Analytics CI/CD](cicd-tools.md) . Вы также можете настроить конвейер CI/CD в [Azure pipelines](set-up-cicd-pipeline.md). Azure Pipelines, чтобы включить более широкие возможности, такие как Управление конвейером, Визуализация и триггеры.

## <a name="next-steps"></a>Дальнейшие шаги

* [Автоматизация сборок, тестов и развертываний задания Azure Stream Analytics с помощью средств CI/CD](cicd-tools.md)
* [Настройка конвейера CI/CD для задания Stream Analytics с помощью Azure Pipelines](set-up-cicd-pipeline.md)
