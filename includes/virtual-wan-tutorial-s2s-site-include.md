---
title: включение файла
description: включение файла
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4bcee1097010bb8746b11185a470ca2584485c3f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488958"
---
1. На странице портала для виртуальной глобальной сети в разделе **Подключение** выберите пункт **Сайты VPN**, чтобы открыть страницу сайтов VPN.
2. На странице **Сайты VPN** щелкните **+Создать сайт**.

   ![Основы](./media/virtual-wan-tutorial-site-include/basics.png "Основы")
3. На странице **Create VPN Site** (Создание сайта VPN) на вкладке **Основные сведения** заполните следующие поля:

    * **Регион**. Ранее этот параметр назывался расположением. Это расположение, в котором необходимо создать этот ресурс сайта.
    * **Имя**. Имя, с помощью которого вы будете ссылаться на свой локальный сайт.
    * **Поставщик устройства**. Имя поставщика устройства VPN (например, Citrix, Cisco, Barracuda). Это поможет команде Azure лучше понять вашу среду, чтобы добавить дополнительные возможности оптимизации в будущем или помочь вам устранить неполадки.
    * **Протокол BGP**. Включение этого параметра подразумевает, что для всех подключений с сайта будет включен протокол BGP. Наконец, в разделе "Ссылки" на сайте VPN вы настроите сведения о BGP для каждой ссылки. В Виртуальной глобальной сети протокол BGP настраивается точно так же, как и в шлюзе виртуальной сети VPN Azure. Локальный узел BGP не может иметь тот же IP-адрес, который используется как общедоступный для VPN-устройства, или диапазон IP-адресов виртуальной сети VPN-сайта. Используйте в качестве IP-адреса узла BGP другой IP-адрес VPN-устройства. Можно использовать адрес интерфейса замыкания на себя. Но это не должен быть APIPA-адрес (169.254.x.x). Укажите этот адрес на соответствующем сайте VPN, который представляет это расположение. Предварительные требования BGP см. статье [About BGP with Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md) (Сведения об использовании BGP с VPN-шлюзом Azure). Вы всегда можете изменить подключение VPN, чтобы обновить его параметры BGP (IP-адрес пиринга ссылки и номер AS), если будет включен параметр BGP сайта VPN.
    * **Частное адресное пространство**. Это пространство IP-адресов, которое находится на локальном сайте. Трафик, предназначенный для этого адресного пространства, перенаправляется на ваш локальный сайт. Оно необходимо, если для сайта не включен параметр BGP.
    * **Концентраторы**. Это концентратор, к которому должен подключиться сайт. Сайт можно подключить только к тем концентраторам, которые имеют VPN-шлюз. Если концентратор не отображается, сначала создайте VPN-шлюз в этом концентраторе.
4. Выберите параметр **Ссылки**, чтобы добавить сведения о физических связях в филиале. Если у вас есть партнерское устройство CPE Виртуальной глобальной сети, обратитесь к партнеру, чтобы узнать, осуществляется ли обмен сведениями с Azure в рамках передачи сведений о филиале, настроенной для систем.

   ![ссылки](./media/virtual-wan-tutorial-site-include/links.png "Ссылки")

    * **Имя ссылки**. Имя, которое необходимо указать для физической связи на сайте VPN. Например, mylink1.
    * **Имя поставщика**. Это имя физической связи на сайте VPN. Пример: ATT, Verizon.
    * **Скорость**. Это скорость VPN-устройства в расположении филиала. Пример: 50, то есть 50 Мбит/с, — это скорость VPN-устройства на сайте филиала.
    * **IP-адрес**. Общедоступный IP-адрес локального устройства, которое использует эту ссылку. При необходимости можно предоставить частный IP-адрес локального VPN-устройства, которое находится за ExpressRoute.
5. Установив флажок, можно удалить или добавить дополнительные ссылки. Поддерживаются четыре ссылки на сайт VPN. Например, если у вас есть четыре поставщика услуг Интернета в филиале, можно создать четыре ссылки — по одной на каждого поставщика, и предоставить сведения для каждой ссылки.
6. Заполнив нужные поля, выберите параметр **Просмотр и создание**, чтобы проверить и создать сайт.
7. Просмотрите состояние на странице сайтов VPN. Сайт перейдет в состояние **Connection Needed** (Требуется подключение), так как он еще не подключен к концентратору.