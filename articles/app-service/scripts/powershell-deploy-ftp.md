---
title: PowerShell. отправка файлов по FTP
description: Сведения об использовании Azure PowerShell для автоматизации процессов развертывания и управления в Службе приложений. В этом примере показано, как отправлять файлы в приложение с помощью FTP.
tags: azure-service-management
ms.assetid: b7d46d6f-44fd-454c-8008-87dab6eefbc1
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 8a214deb0cde5ffd6dee6d31377c3c898a363b27
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073851"
---
# <a name="upload-files-to-a-web-app-using-ftp"></a>Передача файлов в веб-приложение с помощью протокола FTP

Этот пример сценария создает веб-приложение со связанными ресурсами в службе приложений, а затем развертывает код веб-приложения с помощью протокола FTP (посредством [WebClient.UploadFile()](/dotnet/api/system.net.webclient.uploadfile?view=netcore-3.1)).

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве Azure PowerShell](/powershell/azure/), а затем выполните команду `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="sample-script"></a>Пример скрипта

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-ftp/deploy-ftp.ps1?highlight=1 "Upload files to a web app using FTP")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполнив пример сценария, вы можете удалить группу ресурсов, веб-приложение и все связанные ресурсы, используя следующую команду.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Создает план службы приложений. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Создает веб-приложение. |
| [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) | Получает профиль публикации веб-приложения. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/).

Дополнительные примеры сценариев Azure PowerShell для веб-приложений службы приложений Azure доступны [здесь](../samples-powershell.md).
