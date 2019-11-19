---
title: Восстановление удаленных приложений службы приложений в службе приложений Azure
description: Узнайте, как восстановить удаленное приложение службы приложений с помощью PowerShell.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: 6a3a62053a488f95e22cae13ef9d0714a7b5dd05
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173740"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Восстановление удаленного приложения службы приложений с помощью PowerShell

Если вы случайно удалили приложение в службе приложений Azure, его можно восстановить с помощью команд из [модуля AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

## <a name="re-register-app-service-resource-provider"></a>Повторная регистрация поставщика ресурсов службы приложений
Некоторые клиенты могут столкнуться с проблемой, при которой не удается получить список удаленных приложений. Чтобы устранить эту проблему, выполните следующую команду:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Вывод списка удаленных приложений

Чтобы получить коллекцию удаленных приложений, можно использовать `Get-AzDeletedWebApp`.

Дополнительные сведения о конкретном удаленном приложении можно использовать:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

Подробные сведения включают:

- **Делетедситеид**: уникальный идентификатор приложения, используемый для сценариев, в которых несколько приложений с одинаковым именем были удалены
- **SubscriptionID**: подписка, содержащая удаленный ресурс
- **Расположение**: расположение исходного приложения
- **ResourceGroupName**: имя исходной группы ресурсов.
- **Имя**: имя исходного приложения.
- **Слот**— имя слота.
- **Время удаления**: когда приложение было удалено  

## <a name="restore-deleted-app"></a>Восстановить удаленное приложение

После определения приложения, которое вы хотите восстановить, его можно восстановить с помощью `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

Входные данные для команды:

- **Группа ресурсов**: Целевая группа ресурсов, в которую будет восстановлено приложение
- **Имя**: имя приложения должно быть глобально уникальным.
- **Таржетаппсервицепланнаме**: план службы приложений, связанный с приложением

По умолчанию `Restore-AzDeletedWebApp` восстановит как конфигурацию приложения, так и содержимое. Если вы хотите восстановить только содержимое, используйте флаг `-RestoreContentOnly` с этим командлет.

> [!NOTE]
> Если приложение было размещено в, а затем удалено из Среда службы приложений то его можно восстановить только в том случае, если соответствующий Среда службы приложений по-прежнему существует.
>

Полную ссылку на командлет можно найти здесь: [RESTORE-азделетедвебапп](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
