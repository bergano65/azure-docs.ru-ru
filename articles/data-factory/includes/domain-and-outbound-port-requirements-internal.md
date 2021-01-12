---
title: включить файл
description: включить файл
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 45c6bbb88ef1f01f729451af27ecc73244483216
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121672"
---
| Доменные имена                                          | Исходящие порты | Описание                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| Общедоступное облако: `*.servicebus.windows.net` <br> Azure для государственных организаций: `*.servicebus.usgovcloudapi.net` <br> Китае `*.servicebus.chinacloudapi.cn`   | 443            | Требуется локальной среде выполнения интеграции для интерактивной разработки. |
| Общедоступное облако: `{datafactory}.{region}.datafactory.azure.net`<br> или `*.frontend.clouddatahub.net` <br> Azure для государственных организаций: `{datafactory}.{region}.datafactory.azure.us` <br> Китае `{datafactory}.{region}.datafactory.azure.cn` | 443            | Требуется локальной среде выполнения интеграции для подключения к службе фабрики данных. <br>Для новой созданной фабрики данных в общедоступном облаке найдите полное доменное имя в собственном Integration Runtimeном ключе, который находится в формате {факт.}. {Region}. фактическое. Azure. NET. Для старого экземпляра Фабрики данных, если полное доменное имя не отображается в ключе локальной среды IR, используйте *.frontend.clouddatahub.net. |
| `download.microsoft.com`    | 443            | Требуется локальной среде выполнения интеграции для скачивания обновлений. Если автоматическое обновление отключено, можно пропустить настройку этого домена. |
| URL-адрес хранилища ключей | 443           | Требуется для Azure Key Vault, если учетные данные хранятся в Key Vault. |
