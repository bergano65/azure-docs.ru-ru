---
title: Включите решение Azure для VMware CloudSimple службы
description: Описывается, как включить службу CloudSimple для подписки Azure, а затем зарегистрировать поставщик ресурсов CLoudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2553aa95d5028c510b4e1a1b7f51a9f410bcea51
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154864"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Регистрация поставщика ресурсов Microsoft.VMwareCloudSimple в вашей подписке Azure

Служба CloudSimple позволяет использовать решения Azure VMware с CloudSimple. Вы можете зарегистрировать Microsoft.VMwareCloudSimple службы как поставщик ресурсов.

## <a name="register-the-resource-provider"></a>Регистрация поставщика ресурсов

1. Войдите на [портале Azure](https://portal.azure.com).

2. Выбор пункта **Все службы**.

3. Найдите и выберите **подписок**.

    ![Выбор пункта "Подписки"](media/cloudsimple-service-select-subscriptions.png)

4. Выберите подписку, на котором вы хотите включить службу CloudSimple.

5. Нажмите кнопку **поставщиков ресурсов** для подписки.

6. Используйте **Microsoft.VMwareCloudSimple** для фильтрации поставщика ресурсов.

7. Выберите поставщика ресурсов и нажмите кнопку **зарегистрировать**.

    ![Регистрация поставщика ресурсов](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [создать службу CloudSimple](create-cloudsimple-service.md)
* Узнайте, как [Настройка среды с частным облаком](quickstart-create-private-cloud.md)
