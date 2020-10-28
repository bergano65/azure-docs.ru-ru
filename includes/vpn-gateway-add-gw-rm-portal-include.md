---
title: включить файл
description: Включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fe95481e977d2e35c2f652b6c99b9e69ae89e2f8
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92479581"
---
1. На [портале Azure](https://portal.azure.com) в поле **Поиск по ресурсам, службам и документам (G+/)** введите текст **шлюз виртуальной сети** . Найдите **шлюз виртуальной сети** в результатах поиска и выберите его.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/search.png" alt-text="Поле поиска" lightbox="./media/vpn-gateway-add-gw-rm-portal-include/search-expand.png":::

1. На странице **Шлюзы виртуальной сети** нажмите кнопку **+ Добавить** . Откроется страница **Создание шлюза виртуальной сети** .

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/add.png" alt-text="Поле поиска":::
1. На вкладке **Основные сведения** укажите значения для шлюза виртуальной сети.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway.png" alt-text="Поле поиска":::

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway-vnet.png" alt-text="Поле поиска" активный — активный"** следует использовать только в том случае, если вы настраиваете конфигурацию шлюза в режиме "активный — активный". В противном случае оставьте для этого параметра значение **Отключено** .
     * **Не выбирайте** параметр **Configure BGP** (Настроить BGP), кроме случаев, когда его выбор обусловлен используемой конфигурацией. Если этот параметр необходим, по умолчанию для ASN устанавливается значение 65515, которое при необходимости можно изменить.
1. Выберите **Просмотр и создание** , чтобы выполнить проверку.
1. Затем щелкните **Создать** , чтобы развернуть шлюз виртуальной частной сети. Это может занять до 45 минут. Состояние развертывания можно отслеживать на странице обзора шлюза.

После создания шлюза вы можете просмотреть назначенный ему IP-адрес в разделе сведений о виртуальной сети на портале. Шлюз будет отображаться как подключенное устройство.
