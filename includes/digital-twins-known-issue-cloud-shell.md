---
author: baanders
description: добавляемый файл для Azure Digital Twins — упоминание известной проблемы с проверкой подлинности в Cloud Shell
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: 574ca9d42565e897e49d6800e61bb2c33717891b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92321778"
---
>[!NOTE]
>В Cloud Shell есть **известная проблема** , которая затрагивает такие группы команд при выполнении из *https://shell.azure.com* : `az dt route`, `az dt model`, `az dt twin`.
>
>Для устранения этой проблемы можно выполнить любое из следующих действий:
> * запустите `az login` в Cloud Shell перед выполнением команды;
> * откройте панель Cloud Shell на портале Azure и завершите работу Cloud Shell;
>  :::image type="content" source="../articles/digital-twins/media/includes/portal-cloud-shell.png" alt-text="Представление портала Azure: выделенный значок Cloud Shell и окно Cloud Shell в нижней части окна портала":::
> * используйте вместо Cloud Shell [локально CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
>
>Дополнительные сведения об этой проблеме см. в статье [*Устранение неполадок: известные проблемы в Azure Digital Twins*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).