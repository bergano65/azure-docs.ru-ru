---
title: PowerShell. Непрерывное развертывание из GitHub
description: Сведения об использовании Azure PowerShell для автоматизации процессов развертывания и управления в Службе приложений. В этом примере показано, как создать приложение с CI/CD из GitHub.
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: e3193180165ec63f3716a9b875a8244ec7288eb8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083299"
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Создание веб-приложения с непрерывным развертыванием из GitHub

Этот пример скрипта создает веб-приложение в Службе приложений со связанными ресурсами, а затем настраивает [непрерывное развертывание](../deploy-continuous-deployment.md) из репозитория GitHub. Дополнительные сведения о развертывании GitHub без непрерывного развертывания см. в статье [Создание веб-приложения и развертывание кода из GitHub](powershell-deploy-github.md).

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве Azure PowerShell](/powershell/azure/), а затем выполните команду `Connect-AzAccount`, чтобы создать подключение к Azure. Кроме того, убедитесь в следующем.

- Код приложения находится в вашем открытом или закрытомрепозитории GitHub. Чтобы получить автоматические сборки, структурируйте репозиторий в соответствии с таблицей [Prepare your repository](../deploy-continuous-deployment.md#prepare-your-repository) (Подготовка репозитория).
- Вы [создали личный маркер доступа в учетной записи GitHub](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line).

## <a name="sample-script"></a>Пример скрипта

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

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
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Изменяет ресурс в группе ресурсов. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/).

Дополнительные примеры сценариев Azure PowerShell для веб-приложений службы приложений Azure доступны [здесь](../samples-powershell.md).
