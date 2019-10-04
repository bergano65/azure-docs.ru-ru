---
title: Восстановление удаленных приложений службы приложений в службе приложений Azure
description: Узнайте, как восстановить удаленное приложение службы приложений с помощью PowerShell.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: 7b3a21f3cfee806dc94353e0bc6c11e88641ea34
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827524"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Восстановление удаленного приложения службы приложений с помощью PowerShell

Если вы случайно удалили приложение в службе приложений Azure, его можно восстановить с помощью команд из [модуля AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

## <a name="list-deleted-apps"></a>Вывод списка удаленных приложений

Чтобы получить коллекцию удаленных приложений, можно использовать `Get-AzDeletedWebApp`.

Дополнительные сведения о конкретном удаленном приложении можно использовать:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app>
```

Подробные сведения включают:

- **Делетедситеид**: Уникальный идентификатор приложения, используемый для сценариев, в которых несколько приложений с одним и тем же именем были удалены
- **SubscriptionID**: Подписка, содержащая удаленный ресурс
- **Расположение.** Расположение исходного приложения
- **ResourceGroupName**: Имя исходной группы ресурсов
- **Имя**: Имя исходного приложения.
- **Слот**— имя слота.
- **Время удаления**: Когда приложение было удалено  

## <a name="restore-deleted-app"></a>Восстановить удаленное приложение

После определения приложения, которое вы хотите восстановить, его можно восстановить с помощью `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

Входные данные для команды:

- **Группа ресурсов**. Целевая группа ресурсов, в которой будет восстановлено приложение
- **Имя**: Имя приложения должно быть глобально уникальным.
- **Таржетаппсервицепланнаме**: План службы приложений, связанный с приложением

По умолчанию `Restore-AzDeletedWebApp` восстановит как конфигурацию приложения, так и содержимое. Если вы хотите восстановить только содержимое, используйте `-RestoreContentOnly` флаг с этим командлет.

Полный справочник по командлет можно найти здесь: [RESTORE-азделетедвебапп](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
