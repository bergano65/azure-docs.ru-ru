---
title: Защита кластера в Azure обозреватель данных
description: В этой статье описывается, как защитить кластер в Azure обозреватель данных в портал Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: fbc5b18de093b2c91b17fa310c08a5b02b113a22
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406526"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>Защита кластера в Azure обозреватель данных

[Шифрование дисков Azure](/azure/security/azure-security-disk-encryption-overview) помогает защитить данные в соответствии с обязательствами по обеспечению безопасности и соответствия требованиям Организации. Он обеспечивает шифрование томов для дисков операционной системы и данных виртуальных машин кластера. Кроме того, она интегрируется с [Azure Key Vault](/azure/key-vault/) для управления ключами и секретами шифрования дисков и управления ими, а также обеспечивает шифрование всех данных на дисках виртуальной машины в службе хранилища Azure. Параметры безопасности кластера позволяют включить шифрование дисков в кластере.
  
## <a name="enable-encryption-at-rest"></a>Включение шифрования при неактивных подключениях
  
Включение [шифрования неактивных](/azure/security/azure-security-encryption-atrest) данных в кластере обеспечивает защиту хранящихся данных (неактивных). 

1. В портал Azure перейдите к ресурсу кластера Azure обозреватель данных. Под заголовком **Параметры** выберите **Безопасность**. 

    ![Включение шифрования при неактивных параметрах](media/manage-cluster-security/security-encryption-at-rest.png)

1. В окне **Безопасность** выберите **вкл** . для параметра безопасность **шифрования диска** . 

1. Щелкните **Сохранить**.

## <a name="next-steps"></a>Следующие шаги

[Проверка работоспособности кластера](/azure/data-explorer/check-cluster-health)
