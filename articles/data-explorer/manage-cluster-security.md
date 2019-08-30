---
title: Защита кластера в Azure обозреватель данных
description: В этой статье описывается, как защитить кластер в Azure обозреватель данных в портал Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: c6f7e921886a6acdaa31d0f69f57119c339c0b8b
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172588"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>Защита кластера в Azure обозреватель данных

[Шифрование дисков Azure](/azure/security/azure-security-disk-encryption-overview) помогает защитить данные в соответствии с обязательствами по обеспечению безопасности и соответствия требованиям Организации. Он обеспечивает шифрование томов для дисков операционной системы и данных виртуальных машин кластера. Она также интегрируется с [Azure Key Vault](/azure/key-vault/) , которая позволяет управлять ключами и секретами шифрования диска и управлять ими, а затем обеспечить шифрование всех данных на дисках виртуальной машины в службе хранилища Azure. 

Параметры безопасности кластера позволяют включить шифрование дисков в кластере.
  
## <a name="enable-encryption-at-rest"></a>Включение шифрования при неактивных подключениях
  
Включение [шифрования неактивных](/azure/security/fundamentals/encryption-atrest) данных в кластере обеспечивает защиту хранящихся данных (неактивных). 

1. В портал Azure перейдите к ресурсу кластера Azure обозреватель данных. Под заголовком **Параметры** выберите **Безопасность**. 

    ![Включение шифрования при неактивных параметрах](media/manage-cluster-security/security-encryption-at-rest.png)

1. В окне **Безопасность** выберите **вкл** . для параметра безопасность **шифрования диска** . 

1. Щелкните **Сохранить**.
 
> [!NOTE]
> Выберите **Отключить** , чтобы отключить шифрование после его включения.

## <a name="next-steps"></a>Следующие шаги

[Проверка работоспособности кластера](/azure/data-explorer/check-cluster-health)
