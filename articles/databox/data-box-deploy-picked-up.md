---
title: Учебник по отправке Azure Data Box обратно | Документация Майкрософт
description: Сведения о том, как отправить Azure Data Box в Майкрософт
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 1c375370ec6bae2775ad758688825b92cbbbca50
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407037"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Руководство по Отправка Azure Data Box и проверка передачи данных в Azure

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>Отправка Data Box и проверка передачи данных в Azure

::: zone-end

::: zone target="docs"

В этом руководстве объясняется, как отправить Azure Data Box и проверить передачу данных в Azure.

В этом руководстве описано следующее:

> [!div class="checklist"]
> * Предварительные требования
> * Подготовка к отправке
> * Отправка Data Box в Майкрософт
> * Проверка передачи данных в Azure
> * Стирание данных из Data Box

## <a name="prerequisites"></a>Предварительные требования

Перед тем как начать, убедитесь в следующем:

- Вы ознакомились со статьей [Руководство. Копирование данных в Azure Data Box через SMB](data-box-deploy-copy-data.md). 
- Задания копирования выполнены. Подготовка к отправке недоступна, если задания копирования не завершены.

## <a name="prepare-to-ship"></a>Подготовка к отправке

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

После копирования данных устройство подготавливается и отправляется. Когда устройство поступает в центр обработки данных Azure, данные автоматически передаются в Azure.

## <a name="prepare-to-ship"></a>Подготовка к отправке

Прежде чем приступать к отправке, убедитесь, что задания копирования завершены.

1. Перейдите к странице **Подготовка к отправке** в локальном пользовательском веб-интерфейсе и начните подготовку к отправке. 
2. Отключите устройство от локального веб-интерфейса. Отсоедините кабели от устройства. 

Дальнейшие действия зависят от того, в каком регионе вы возвращаете устройство.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Отправка Data Box

Убедитесь, что копирование данных на устройство завершено, и **Подготовка к отправке** прошла успешно. В зависимости от региона, в который вы отправляете устройство, процедура отличается.

::: zone-end

## <a name="in-us-canada-europetabin-us-canada-europe"></a>[В США, Канаде, Европе](#tab/in-us-canada-europe)

Если вы возвращаете устройство в США или Канаде, выполните следующие действия:

1. Убедитесь, что устройство отключено и кабели отсоединены. 
2. Смотайте и аккуратно разместите шнур питания, входящий в комплект устройства, на задней панели устройства.
3. Убедитесь, что транспортная этикетка отображается на дисплее E-ink и запланируйте отправку со своим курьером. Если метка повреждена, утеряна или не отображается на дисплее E-ink, обратитесь в службу поддержки Майкрософт. На портале Azure выберите **Обзор > Скачать транспортную этикетку**, если это посоветует специалист службы поддержки. Скачайте транспортную этикетку и прикрепите ее на устройство. 
4. Запланируйте визит курьера UPS для отправки посылки, если возвращаете устройство. Чтобы запланировать отправку с курьером:

    - Позвоните в местное отделение UPS (бесплатный номер конкретной страны/региона).
    - При вызове укажите обратный номер для отслеживания доставки, как показано на дисплее E-ink или на напечатанной этикетке.
    - Если номер для отслеживания не указан, UPS потребует дополнительную плату во время приема посылки.

    Вместо планирования отправки с курьером вы также можете оставить Data Box в ближайшем отделении этой курьерской службы.
4. После того как курьер примет и проверит Data Box, состояние заказа на портале обновится до **Принято курьером**. Также отображается идентификатор отслеживания.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Проверка передачи данных в Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Стирание данных из Data Box
 
Когда передача данных в Azure завершится, Data Box удалит данные с дисков согласно рекомендациям [NIST SP 800-88 в редакции 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]



::: zone-end


## <a name="in-australiatabin-australia"></a>[В Австралии](#tab/in-australia)

В центрах обработки данных Azure в Австралии предусмотрена процедура предварительного уведомления о посылке. Об отправке посылки необходимо заранее предупреждать. Выполните следующие действия для отправки в Австралии.


1. Сохраните оригинальную коробку, использованную для доставки устройства, для обратной отправки.
2. Убедитесь, что копирование данных на устройство завершено и **Подготовка к отправке** прошла успешно.
3. Выключите устройство и отсоедините кабели.
4. Смотайте и аккуратно разместите шнур питания, входящий в комплект устройства, на задней панели устройства.
5. Закажите через Интернет возврат по ссылке [DHL](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Проверка передачи данных в Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Стирание данных из Data Box
 
Когда передача данных в Azure завершится, Data Box удалит данные с дисков согласно рекомендациям [NIST SP 800-88 в редакции 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="in-japantabin-japan"></a>[В Японии](#tab/in-japan) 

1. Сохраните оригинальную коробку, использованную для доставки устройства, для обратной отправки.
2. Выключите устройство и отсоедините кабели.
3. Смотайте и аккуратно разместите шнур питания, входящий в комплект устройства, на задней панели устройства.
4. Укажите название и адрес своей компании в качестве сведений об отправителе в квитанции.
5. Отправьте электронное письмо в компанию Quantium Solutions с помощью указанного ниже шаблона электронного письма.

    - Если квитанция при доставке наложенным платежом Japan Post отсутствует, сообщите об этом в письме. В компании Quantium Solutions распорядятся, чтобы служба Japan Post организовала отправку посылки и оформила квитанцию.
    - Если у вас несколько заказов, отправьте отдельное электронное письмо для каждого из них.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

3. Компания Quantium Solutions отправит подтверждение по электронной почте после оформления отправки. В подтверждении по электронной почте также содержатся сведения о квитанции при доставке наложенным платежом.

При необходимости вы можете обратиться в службу поддержки Quantium Solution (японский язык), используя эти сведения: 

- Электронная почта: Customerservice.JP@quantiumsolutions.com 
- Телефон: 03-5755-0150 

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Проверка передачи данных в Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Стирание данных из Data Box
 
Когда передача данных в Azure завершится, Data Box удалит данные с дисков согласно рекомендациям [NIST SP 800-88 в редакции 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end



