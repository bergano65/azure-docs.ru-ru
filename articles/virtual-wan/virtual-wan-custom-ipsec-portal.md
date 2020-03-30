---
title: 'Нанастройка пользовательской политики IPsec для Azure Virtual WAN: Портал Документы Майкрософт'
description: Узнайте, как настроить пользовательскую политику IPsec для Azure Virtual WAN с помощью портала.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515750"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Налаживание пользовательской политики IPsec для Виртуального WAN с помощью портала

Можно настроить пользовательскую политику IPsec для Virtual WAN на портале Azure. Пользовательские политики полезны, если вы хотите, чтобы обе стороны (на территории и VPN шлюз Azure) использовали одни и те же настройки для IKE Phase 1 и IKE Phase 2.

## <a name="working-with-custom-policies"></a>Работа с пользовательскими политиками

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Настройка политики

1. **Найдите виртуальный концентратор.** Из браузера перейдите на [портал Azure](https://aka.ms/azurevirtualwanpreviewfeatures) и войдите в систему с помощью учетной записи Azure. Найдите виртуальный концентратор для вашего сайта.
2. **Выберите сайт VPN**. На странице концентратора выберите VPN-сайт, для которого вы хотите настроить пользовательскую политику.

   ![select](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Отснатите VPN-соединение.** Из **меню Context** **...**, выберите **Edit VPN Connection**.

   ![изменение;](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Настройка настроек.** На странице **подключения Edit VPN** настроите настройки настроек. Выберите **Сохранить,** чтобы сохранить настройки.

   ![настроить и сохранить](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Виртуальной глобальной сети см. в [этой статье](virtual-wan-about.md).