---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1199819d274590cc81d0234680f8765f9cc36c0a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185123"
---
### <a name="noconnection"></a>Изменение префиксов IP-адресов для шлюза локальной сети при отсутствии подключения шлюза

#### <a name="to-add-additional-address-prefixes"></a>Чтобы добавить дополнительные префиксы адресов:

1. В ресурсе "Шлюз локальной сети" в разделе **Параметры** щелкните **Конфигурации**.
2. Добавьте пространство IP-адресов в поле *Добавить дополнительный диапазон адресов*.
3. Нажмите кнопку **Save** (Сохранить), чтобы сохранить настройки.

#### <a name="to-remove-address-prefixes"></a>Чтобы удалить префиксы адресов, используйте фрагмент кода ниже.

1. В ресурсе "Шлюз локальной сети" в разделе **Параметры** щелкните **Конфигурации**.
2. Нажмите кнопку **...** в строке с префиксом, который нужно удалить.
3. Нажмите кнопку **Удалить**.
4. Нажмите кнопку **Save** (Сохранить), чтобы сохранить настройки.

### <a name="withconnection"></a>Изменение префиксов IP-адресов для шлюза локальной сети при наличии подключения шлюза

Если шлюз уже подключен и вам нужно добавить или удалить префиксы IP-адресов, указанные для локального сетевого шлюза, выполните приведенные ниже шаги именно в такой последовательности. После этого VPN-подключение будет некоторое время недоступно. При изменении префиксов IP-адресов не нужно удалять VPN-шлюз. Необходимо удалить только подключение.

#### <a name="1-remove-the-connection"></a>1. Удалите подключение.

1. В ресурсе "Шлюз локальной сети" в разделе **Параметры** щелкните **Подключения**.
2. Нажмите кнопку **...** в строке для каждого подключения, а затем — кнопку **Удалить**.
3. Нажмите кнопку **Save** (Сохранить), чтобы сохранить настройки.

#### <a name="2-modify-the-address-prefixes"></a>2. Изменение префиксов адресов.

Чтобы добавить дополнительные префиксы адресов:

1. В ресурсе "Шлюз локальной сети" в разделе **Параметры** щелкните **Конфигурации**.
2. Добавьте пространство IP-адресов.
3. Нажмите кнопку **Save** (Сохранить), чтобы сохранить настройки.

Чтобы удалить префиксы адресов, используйте фрагмент кода ниже.

1. В ресурсе "Шлюз локальной сети" в разделе **Параметры** щелкните **Конфигурации**.
2. Нажмите кнопку **...** в строке с префиксом, который нужно удалить.
3. Нажмите кнопку **Удалить**.
4. Нажмите кнопку **Save** (Сохранить), чтобы сохранить настройки.

#### <a name="3-recreate-the-connection"></a>3. Повторное создание подключения.

1. Перейдите к шлюзу вашей виртуальной сети (не шлюзу локальной сети).
2. В ресурсе "Шлюз виртуальной сети" в разделе **Параметры** щелкните **Подключения**.
3. Щелкните **+Добавить**, чтобы открыть колонку **Добавление подключения**.
4. Заново создайте подключение.
5. Нажмите кнопку **ОК**, чтобы создать подключение.