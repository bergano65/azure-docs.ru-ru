---
title: Руководство по созданию подключения типа "точка — сеть" с помощью Виртуальной глобальной сети Azure
description: В руководстве показано, как создать VPN-подключение типа "точка — сеть" в Azure с помощью Виртуальной глобальной сети Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: cherylmc
ms.openlocfilehash: 3d03d0267ff4fb16042d5cc2016e87139b88281a
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056588"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Руководство по созданию пользовательского соединения VPN с помощью Виртуальной глобальной сети Azure

В этом руководстве показано, как создать подключение к ресурсам в Azure через VPN-соединение IPsec/IKE (IKEv2) или OpenVPN с помощью Виртуальной глобальной сети. Этот тип подключения требует, чтобы клиент был настроен на клиентском компьютере. Дополнительные сведения о Виртуальной глобальной сети см. в [этой статье](virtual-wan-about.md).

В этом руководстве описано следующее:

> [!div class="checklist"]
> * Создание глобальной сети.
> * Создание конфигурации подключения "точка — сеть"
> * Создание концентратора.
> * Указание DNS-серверов
> * Скачивание профиля VPN-клиента
> * Просмотр виртуальной глобальной сети

![Схема Виртуальной глобальной сети](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Создание виртуальной глобальной сети

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Создание конфигурации подключения типа "точка — сеть"

Конфигурация подключения типа "точка — сеть" (P2S) определяет параметры для подключения удаленных клиентов.

[!INCLUDE [Create client profiles](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Создание концентратора с шлюзом типа "точка — сеть"

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>Указание DNS-сервера

Виртуальные частные шлюзы виртуальной глобальной сети позволяют указать до пяти DNS-серверов. Их можно настроить при создании концентратора или изменить позднее. Для этого найдите нужный виртуальный концентратор. В разделе **VPN пользователя (точка — сеть)** щелкните действие **настройки** и введите IP-адреса DNS-серверов в текстовые поля **Пользовательские DNS-серверы**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="Пользовательские DNS-серверы" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>Загрузка профиля VPN

Используйте профиль VPN для настройки клиентов.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

### <a name="configure-user-vpn-clients"></a>Настройка клиентов VPN пользователя

Используйте загруженный профиль для настройки клиентов удаленного доступа. Процедуры для каждой операционной системы отличаются, поэтому следуйте инструкциям, применимым к вашей операционной системе.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Просмотр виртуальной глобальной сети

1. Перейдите к виртуальной глобальной сети.
1. На странице **Обзор** каждая точка на карте представляет собой концентратор.
1. В разделе **Концентраторы и подключения** можно просмотреть сведения о состоянии концентратора, сайте, регионе, состоянии VPN-подключения, а также количестве принятых и переданных байтов.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы с помощью командлета [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Замените myResourceGroup на имя вашей группы ресурсов и выполните следующую команду PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Виртуальной глобальной сети см. в следующей статье:

> [!div class="nextstepaction"]
> * [Вопросы и ответы о Виртуальной глобальной сети](virtual-wan-faq.md)
