---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150741"
---
1. Войдите на устройство Data Box. Убедитесь, что оно разблокировано.

    ![Панель мониторинга Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Перейдите к **Задать сетевые интерфейсы**. Запишите IP-адрес устройства для сетевого интерфейса, используемого для подключения к клиенту.

    ![Панель мониторинга Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Перейдите в пункт меню **Подключиться и копировать** и щелкните **Rest**.

    ![Панель мониторинга Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. В диалоговом окне **Доступ к учетной записи хранения и отправка данных** скопируйте **конечную точку службы BLOB-объектов**.

    ![Панель мониторинга Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Откройте **Блокнот** от имени администратора, а затем откройте файл **hosts**, расположенный в `C:\Windows\System32\Drivers\etc`.
6. Добавьте следующую запись в ваш файл **hosts**: `<device IP address> <Blob service endpoint>`
7. Для ссылки используйте следующее изображение. Сохраните файл **hosts**.

    ![Панель мониторинга Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
