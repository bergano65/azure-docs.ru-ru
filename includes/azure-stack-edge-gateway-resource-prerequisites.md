---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 354811b1db31731913f2a99287b0e4a71b2aff61
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464821"
---
Перед тем как начать, убедитесь в следующем.

* Подписка Microsoft Azure включена для ресурса Azure Stack Edge. Убедитесь, что используется поддерживаемая подписка, например, подписка с [Соглашением Microsoft Enterprise (EA)](https://azure.microsoft.com/overview/sales-number/), подписка в рамках программы [Поставщик облачных решений (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp) или подписка со [спонсорским предложением Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/).
* У вас должен быть доступ с правами владельца или участника на уровне группы ресурсов к ресурсам Azure Stack Edge или шлюза службы хранилища Azure, Центра Интернета вещей и службы хранилища Azure.

  * Для создания ресурсов Azure Stack Edge необходимы разрешения с правами участника (как минимум), действующие на уровне группы ресурсов. Необходимо также убедиться, что зарегистрирован поставщик `Microsoft.DataBoxEdge`. Сведения о том, как выполнить регистрацию, см. в разделе о [регистрации поставщика ресурсов](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Для создания ресурсов Центра Интернета вещей нужно зарегистрировать поставщик Microsoft.Devices. Сведения о том, как выполнить регистрацию, см. в разделе о [регистрации поставщика ресурсов](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Чтобы создать ресурс учетной записи хранения, также необходимы разрешения с правами, начиная с участника, действующие на уровне группы ресурсов. Хранилище Azure по умолчанию является зарегистрированным поставщиком ресурсов.
* У вас есть доступ к API Graph Azure Active Directory в роли администратора или пользователя. Дополнительные сведения см. в статье [Области разрешений | Основные понятия API Graph](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Имеется учетная запись хранения Microsoft Azure и данные для доступа к ней.

