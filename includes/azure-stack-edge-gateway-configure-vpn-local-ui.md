---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467375"
---
Выполните следующие действия в локальном веб-ИНТЕРФЕЙСе устройства. Этот шаг занимает около 15 минут, включая отправку файла конфигурации VPN (или файла тегов службы). 

1. Перейдите в раздел **Configuration > VPN**. Нажмите кнопку **Настроить**.

    ![Настройка локального пользовательского интерфейса 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. В колонке **Настройка VPN** :

    - Включите **параметры VPN**.
    - Укажите **общий секрет VPN**. Это общий ключ, предоставленный при создании ресурса VPN-подключения Azure.
    - Укажите **IP-адрес VPN-шлюза** . Это IP-адрес шлюза локальной сети Azure.
    - Для **группы PFS** выберите **нет**. 
    - Для **группы DH** выберите **группа2**.
    - В качестве **метода целостности IPSec** выберите **SHA256**.
    - Для **констант преобразования шифра IPsec** выберите **GCMAES256**.
    - Для **констант преобразования проверки подлинности IPsec** выберите **GCMAES256**.
    - В качестве **метода шифрования IKE** выберите **AES256**.
    - Нажмите кнопку **Применить**.

        ![Настройка локального пользовательского интерфейса 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    Дополнительные сведения о поддерживаемых алгоритмах шифрования см. в статье [требования к шифрованию и VPN-шлюзы Azure](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq). 

3. Чтобы отправить файл конфигурации VPN-маршрута, нажмите кнопку **Отправить**. 

    ![Настройка локального пользовательского интерфейса 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - Перейдите *к файлу тегов* службы, который был скачан в локальной системе на предыдущем шаге.
    - Выберите регион в качестве региона Azure, связанного с устройством, виртуальной сетью и шлюзами.
    - Нажмите кнопку **Применить**.

        ![Настройка локального пользовательского интерфейса 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    Отправка занимает около 7-8 минут на устройстве.

4. Чтобы добавить клиентские маршруты, настройте доступ к диапазонам IP-адресов, используя только VPN. 

    - В разделе **диапазоны IP-адресов для доступа с использованием только VPN** выберите **настроить**.
    - Укажите допустимый диапазон IPv4-адресов и нажмите кнопку " **Добавить**". Повторите шаги, чтобы добавить другие диапазоны.
    - Нажмите кнопку **Применить**.

        ![Настройка локального пользовательского интерфейса 5](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

