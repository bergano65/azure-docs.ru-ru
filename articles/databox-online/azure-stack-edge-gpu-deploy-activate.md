---
title: Учебник по активации устройства Azure Stack Edge с GPU на портале Azure | Документация Майкрософт
description: В учебнике по развертыванию GPU Azure Stack Edge вам предлагается активировать физическое устройство.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 88be4d9753e48f70dae5666e800a54209ed6ba3f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267944"
---
# <a name="tutorial-activate-azure-stack-edge-with-gpu"></a>Руководство по активации Azure Stack Edge с GPU

В этом учебнике описывается, как активировать устройство Azure Stack Edge со встроенным GPU, используя локальный пользовательский веб-интерфейс.

Процесс активации может занять около 5 минут.

Из этого учебника вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Предварительные требования
> * Активация физического устройства

## <a name="prerequisites"></a>Предварительные требования

Перед установкой и настройкой устройства Azure Stack Edge с GPU проверьте следующие условия:

* Для физического устройства: 
    
    - Вы установили физическое устройство, как описано в статье [Руководство по установке Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
    - Вы настроили сеть и параметры вычислительной сети, как описано в статье [Учебник. Настройка сети для Azure Stack Edge с GPU](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
    - Вы загрузили собственные или созданные сертификаты устройства на своем устройстве, когда изменили имя устройства или домена DNS на странице **Устройства**. Если вы не выполнили этот шаг, во время активации устройства вы увидите сообщение об ошибке, и активация будет заблокирована. Дополнительные сведения см. в статье [Учебник. Настройка сертификатов для Azure Stack Edge с GPU](azure-stack-edge-gpu-deploy-configure-certificates.md).
    
* У вас есть ключ активации из службы Azure Stack Edge, который вы создали для управления устройством Azure Stack Edge. Дополнительные сведения см. в разделе [Подготовка к развертыванию Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md).


## <a name="activate-the-device"></a>Активация устройства

1. В локальном веб-интерфейсе устройства перейдите на страницу **Начало работы**.
2. На плитке **Активация** выберите **Активировать**. 

    ![Страница Cloud details (Сведения об облаке) в локальном пользовательском веб-интерфейсе](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. В области **Активация** в поле **Ключ активации** введите ключ, который вы получили при работе с разделом [Получение ключа активации](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).

4. Нажмите кнопку **Применить**.

    ![Страница Cloud details (Сведения об облаке) в локальном пользовательском веб-интерфейсе](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. Сначала устройство активируется. Затем вам будет предложено скачать файл ключа.
    
    ![Страница Cloud details (Сведения об облаке) в локальном пользовательском веб-интерфейсе](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    Выберите **Download key file** (Скачать файл ключа) и сохраните файл *keys.json* в безопасном расположении вне устройства. **Этот файл ключа содержит ключи восстановления для диска ОС и дисков данных на вашем устройстве.** Вот содержимое файла *keys.json*:

        
    ```json
    {
      "Id": "1ab3fe39-26e6-4984-bb22-2e02d3fb147e",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "C086yg1DrPo0DuZB/a7hUh+kBWj804coJfBA9LDzZqw=",
        "hcsdata": "8ohX9bG3YSZl9DZmZLkYl//L9dXi1XiQrqza+iSd64Q="
      },
      "SystemVolumeBitLockerRecoveryKey": "105347-156739-594473-151107-005082-252604-471955-439395",
      "ServiceEncryptionKey": "oEwxNJeULzGRFt6DsLgcLw=="
    }
    ```
        
 
    Различные ключи описаны в следующей таблице.
    
    |Поле  |Описание  |
    |---------|---------|
    |`Id`    | Это идентификатор устройства.        |
    |`DataVolumeBitLockerExternalKeys`|Это ключи BitLockers для дисков данных, которые используются для восстановления локальных данных на устройстве.|
    |`SystemVolumeBitLockerRecoveryKey`| Это ключ BitLocker для системного тома. Этот ключ помогает восстановить конфигурацию системы и системные данные для устройства. |
    |`ServiceEncryptionKey`| Этот ключ защищает данные, проходящие через службу Azure. Этот ключ гарантирует, что компрометация службы Azure не приведет к компрометации хранимой информации. |

6. Перейдите на страницу **Обзор**. Состояние устройства должно отображаться как **Активировано**.

    ![Страница Cloud details (Сведения об облаке) в локальном пользовательском веб-интерфейсе](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
Активация устройства завершена. Теперь в него можно добавить общие папки.


## <a name="next-steps"></a>Дальнейшие действия

Из этого учебника вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Предварительные требования
> * Активация физического устройства

Чтобы узнать, как передавать данные с помощью устройства Azure Stack Edge, перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Учебник. Передача данных через общие ресурсы с помощью GPU Azure Stack Edge](./azure-stack-edge-j-series-deploy-add-shares.md)
