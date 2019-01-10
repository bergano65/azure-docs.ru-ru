---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 0a9aaa86d44e71e429f2bfff13a56ddcb1ee2071
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550359"
---
1. Войдите на устройство Data Box. Убедитесь, что оно разблокировано.

    ![Панель мониторинга Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Перейдите к **Задать сетевые интерфейсы**. Запишите IP-адрес устройства для сетевого интерфейса, используемого для подключения к клиенту.

    ![Панель мониторинга Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Перейдите к **Подключиться и копировать** и щелкните **REST (предварительная версия)**.

    ![Панель мониторинга Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. В диалоговом окне **Доступ к учетной записи хранения и отправка данных** скопируйте **конечную точку службы BLOB-объектов**.

    ![Панель мониторинга Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Откройте **Блокнот** от имени администратора, а затем откройте файл **hosts**, расположенный в `C:\Windows\System32\Drivers\etc`.
6. Добавьте следующую запись в ваш файл **hosts**: `<device IP address> <Blob service endpoint>`
7. Для ссылки используйте следующее изображение. Сохраните файл **hosts**.

    ![Панель мониторинга Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
