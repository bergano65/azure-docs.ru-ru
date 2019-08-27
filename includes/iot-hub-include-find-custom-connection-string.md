---
title: включение файла
description: включение файла
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050407"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Чтобы создать политику общего доступа, которая предоставляет разрешения **Connect** и для **чтения реестра** , а затем получить строку подключения для этой политики, выполните следующие действия.

1. В [портал Azure](https://portal.azure.com)выберите **группы ресурсов**. Выберите группу ресурсов, в которой находится центр, а затем выберите центр из списка ресурсов.

1. В области слева в центре выберите **политики общего доступа**.

1. В верхнем меню над списком политик выберите **Добавить**.

1. В разделе **Добавление политики общего доступа**введите описательное имя политики, например *сервицеандрегистриреад*. В разделе **разрешения**выберите **Реестр чтение** и **Подключение к службе**, а затем щелкните **создать**.

    ![Покажите, как добавить новую политику общего доступа](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Выберите новую политику в списке политик.

1. В разделе **ключи общего доступа**выберите значок копирования для **строки подключения — первичный ключ** и сохраните значение.

    ![Получение строки подключения](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Дополнительные сведения о политиках и разрешениях общего доступа центра Интернета вещей см. в разделе [Управление доступом и разрешения](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
