---
title: включить файл
description: включить файл
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 04/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 4a6d9ef04a33c84d68dff1429fb39a193a249280
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204595"
---
Эти примеры иллюстрируют различные задачи, которые можно выполнять в межклиентских сценариях управления.

| **Шаблон** | **Описание** |
|---------|---------|
| [create-keyvault-secret](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret) | Создает хранилище ключей в арендаторе клиента и политики доступа.
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | Развертывание учетных записей хранения в двух разных группах ресурсов.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | Создание служб управления Azure, их связывание и развертывание дополнительных решений. Шаблон **rgWithAzureMgmt.json** поможет выполнить комплексное развертывание. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | Включение и настройка Центра безопасности Azure в целевой подписке Azure. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | Развертывание и включение Azure Sentinel в существующей рабочей области Log Analytics в делегированной подписке. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | Эти шаблоны позволяют развертывать расширения Log Analytics для виртуальных машин на виртуальных машинах Windows и Linux, подключив их к назначенной рабочей области Log Analytics. |
