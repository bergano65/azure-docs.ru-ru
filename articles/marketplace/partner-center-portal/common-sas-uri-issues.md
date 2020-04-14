---
title: Общие проблемы и исправления SAS URI - Azure Marketplace
description: Общие проблемы возникают и предлагаются решения при работе с общими подписями доступа.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266244"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Общие проблемы и исправления SAS URI

> [!IMPORTANT]
> Мы перемещаем управление вашими предложениями azure Virtual Machine с облачного партнерского портала в партнерский центр. До тех пор, пока ваши предложения не будут перенесены, пожалуйста, следуйте инструкциям в [общих проблемах SAS URI и исправлениям](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) для Облачного partner Portal для управления вашими предложениями.

Ниже приведены общие проблемы, возникающие при работе с общими подписями доступа (которые используются для идентификации и обмена загруженными VHD для вашего решения), а также предлагаемых резолюций.

| **Проблема** | **Сообщение об ошибке** | **Исправить** |
| --------- | ------------------- | ------- |
| *Ошибка при копировании образов* |  |  |
| "?" не найдено в SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновление SAS URI с использованием рекомендуемых инструментов. |
| Параметры "st" и "se" не в SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновление SAS URI с надлежащей **даты начала** и даты **окончания** значений. |
| "sp'rl" не в SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновление SAS URI с разрешениями, установленными как `Read` и `List`. |
| SAS URI имеет пробелы в названии VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновление SAS URI для удаления белых пространств. |
| Ошибка авторизации SAS URI | `Failure: Copying Images. Not able to download blob due to authorization error.` | Просмотрите и исправьте формат универсального кода ресурса SAS. При необходимости повторно создайте. |
| Параметры SAS URI "st" и "se" не имеют полной спецификации времени даты | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | Параметры **SAS** URI Дата начала`st` и `se` **дата окончания** (и подстроки) должны `11-02-2017T00:00:00Z`иметь полный формат времени даты, например. Укороченные версии недействительны (некоторые команды в Azure CLI могут генерировать сокращенные значения по умолчанию). |
|  |  |  |

Для получения подробной информации [см. Использование общих подписей доступа (SAS).](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
