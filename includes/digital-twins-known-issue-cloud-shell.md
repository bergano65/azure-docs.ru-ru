---
author: baanders
description: добавляемый файл для Azure Digital Twins — упоминание известной проблемы с проверкой подлинности в Cloud Shell
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: bd3a192ed493a886da427cb6d89239a4eb8e4d17
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89290109"
---
>[!NOTE]
>В Cloud Shell присутствует **известная проблема**, которая затрагивает такие группы команд: `az dt route`, `az dt model`, `az dt twin`.
>
>Чтобы устранить эту проблему, выполните `az login` в Cloud Shell перед выполнением команды или используйте [локальный CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) вместо Cloud Shell. Дополнительные сведения см. в статье [*Устранение неполадок: известные проблемы в Azure Digital Twins*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).