---
title: 'Настройка настраиваемой политики IPsec для виртуальной глобальной сети Azure: портал | Документация Майкрософт'
description: Узнайте, как настроить пользовательскую политику IPsec для виртуальной глобальной сети Azure с помощью портала.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "73515750"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Настройка настраиваемой политики IPsec для виртуальной глобальной сети с помощью портала

Вы можете настроить пользовательскую политику IPsec для виртуальной глобальной сети в портал Azure. Пользовательские политики полезны, когда нужно, чтобы обе стороны (локальный и VPN-шлюз Azure) использовали одни и те же параметры для фазы IKE 1 и фазы IKE 2.

## <a name="working-with-custom-policies"></a>Работа с пользовательскими политиками

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Настройка политики

1. **Нахождение виртуального концентратора**. В браузере откройте [портал Azure](https://aka.ms/azurevirtualwanpreviewfeatures) и выполните вход с помощью учетной записи Azure. Поиск виртуального концентратора для сайта.
2. **Выберите VPN-сайт**. На странице Центр выберите VPN-сайт, для которого требуется настроить настраиваемую политику.

   ![select](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Измените VPN-подключение**. В **контекстном меню** **...** выберите **изменить VPN-подключение**.

   ![изменение;](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Настройте параметры**. На странице **изменение VPN-подключения** настройте параметры. Нажмите кнопку **сохранить** , чтобы сохранить параметры.

   ![Настройка и сохранение](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Виртуальной глобальной сети см. в [этой статье](virtual-wan-about.md).