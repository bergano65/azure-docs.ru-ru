---
title: Примеры для Azure PowerShell. Сброс Облачных служб Azure (расширенная поддержка)
description: Примеры скриптов для сброса развертывания Облачной службы Azure (расширенная поддержка)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 40b44fd277eac14a5bf2c15f58fccfd9d5b156c4
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881490"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Сброс Облачной службы Azure (расширенная поддержка) 
В этих примерах рассматриваются различные способы для сброса существующего развертывания Облачной службы Azure (расширенная поддержка).

## <a name="reimage-role-instances-of-cloud-service"></a>Пересоздание образов для экземпляров ролей Облачной службы
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances -Reimage
```
Эта команда пересоздает образы для 2 экземпляров ролей **ContosoFrontEnd\_IN\_0** и **ContosoBackEnd\_IN\_1** Облачной службы с именем ContosoCS, которая принадлежит группе ресурсов с именем ContosOrg.

## <a name="reimage-all-roles-of-cloud-service"></a>Пересоздание образа для всех ролей Облачной службы
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Reimage
```

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Пересоздание образа для отдельного экземпляра ролей Облачной службы
```powershell
Reset-AzCloudServiceRoleInstance -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0" -Reimage
```

## <a name="restart-a-single-role-instance-of-a-cloud-service"></a>Перезапуск отдельного экземпляра ролей Облачной службы
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Restart
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения об Облачных службах Azure (расширенная поддержка) см. в статье [Общие сведения об Облачных службах Azure (расширенная поддержка)](overview.md).
- Перейдите в [репозиторий примеров для Облачных служб (расширенная поддержка) ](https://github.com/Azure-Samples/cloud-services-extended-support)