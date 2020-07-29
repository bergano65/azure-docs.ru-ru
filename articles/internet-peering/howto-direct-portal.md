---
title: Создание или изменение прямого пиринга с помощью портала Azure
titleSuffix: Azure
description: Создание или изменение прямого пиринга с помощью портала Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: e49462612b58163c2ac51b78584761d0d8b8bd06
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84700571"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Создание или изменение прямого пиринга с помощью портала Azure

Узнайте, как создать прямой пиринг Майкрософт для поставщика услуг Интернета или поставщика услуг обмена интернет-трафиком с помощью портала Azure. Также здесь показано, как проверить состояние ресурса, обновить его или удалить и отозвать.

Все инструкции из этого руководства можно также выполнить с помощью [Azure PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Перед началом
* Прежде чем начать настройку, изучите [предварительные требования](prerequisites.md) и [пошаговое руководство по прямому пирингу](walkthrough-direct-all.md).
* Если у вас уже есть подключения прямого пиринга Майкрософт, которые не преобразованы в ресурсы Azure, см. статью [Преобразование устаревшего прямого пиринга в ресурс Azure с помощью портала Azure](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Создание прямого пиринга и его подготовка к работе

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Вход на портал и выбор подписки
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Создание прямого пиринга

Поставщик услуг Интернета или поставщик услуг обмена интернет-трафиком может [создать запрос на новое пиринговое подключение]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. На странице **Create a Peering** (Создание пиринга) заполните поля на вкладке **Основные**, как показано ниже.


    ![Регистрация в Службе пиринга](./media/setup-basics-tab.png)

2. Выберите свою подписку Azure.

3. Для параметра "Группа ресурсов" можно выбрать существующую группу ресурсов в раскрывающемся списке или создать новую, щелкнув "Создать". В этом примере показано, как создать новую группу ресурсов.

4. Имя соответствует имени ресурса и может быть любым.

5. При выборе существующей группы ресурсов регион выбирается автоматически. Если вы решили создать новую группу ресурсов, необходимо также выбрать регион Azure, где будет размещаться этот ресурс.

    >[!NOTE]
    > Регион, в котором находится группа ресурсов, не зависит от расположения, в котором вы хотите создать пиринг с Майкрософт. Ресурсы пиринга рекомендуется размещать в группах ресурсов, которые находятся в ближайших регионах Azure. Например, для пиринга в городе Эшберн можно создать группу ресурсов в регионах "Восточная часть США" или "Восточная часть США 2".

6. Выберите значение ASN в поле **PeerASN**.

    >[!IMPORTANT]
    >Перед отправкой запроса на пиринг можно выбрать только ASN с параметром ValidationState, которому присвоено значение "Утверждено". После отправки запроса PeerAsn следует подождать 12 часов, пока будет утверждена связь с ASN. Если после выбора ASN все еще ожидается проверка, отобразится сообщение об ошибке. Если вы не видите ASN для выбора, убедитесь, что выбрана правильная подписка. Если это так, убедитесь, что вы уже создали PeerAsn, **[связав PeerAsn с подпиской Azure](https://go.microsoft.com/fwlink/?linkid=2129592)** .

7. По завершении выберите **Next: Конфигурация**, чтобы продолжить.



    ![Регистрация в Службе пиринга](./media/setup-direct-basics-filled-tab.png)


#### <a name="configure-connections-and-submit"></a>Настройка подключений и отправка
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Проверка прямого пиринга
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Изменение прямого пиринга
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Отзыв прямого пиринга
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение пиринга по обмену интернет-трафика с помощью портала](howto-exchange-portal.md)
* [Преобразование устаревшего пиринга по обмену интернет-трафика в ресурс Azure с помощью портала](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения см. в статье [Часто задаваемые вопросы об интернет-пиринге](faqs.md).
