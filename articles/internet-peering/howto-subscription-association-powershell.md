---
title: Ассоциированное одноранговое снасть ASN для подписки Azure с помощью PowerShell
titleSuffix: Azure
description: Ассоциированное одноранговое снасть ASN для подписки Azure с помощью PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 77cc4732e017d95cbae19578cf26b1111b08fdde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75908984"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>Ассоциированное одноранговое снасть ASN для подписки Azure с помощью PowerShell

Прежде чем отправить запрос на пиринг, сначала следует связать asN с подпиской Azure, используя приведенные ниже шаги.

Если вы предпочитаете, вы можете завершить это руководство с помощью [портала](howto-subscription-association-portal.md).

### <a name="working-with-azure-powershell"></a>Работа с Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Создайте PeerASN, чтобы связать asN с подпиской Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Войдите в учетную запись Azure и выберите подписку.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>Регистрация для поставщика пиринговых ресурсов
Зарегистрируйтесь для поставщика пиринговых ресурсов в подписке, используя приведенную ниже команду. Если вы не выполняете это, то ресурсы Azure, необходимые для настройки пиринга, недоступны.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

Вы можете проверить статус регистрации, используя команды ниже:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Подождите, пока *РегистрацияГосударство* повернет "Зарегистрированный" перед началом работы. После выполнения команды может потребоваться от 5 до 30 минут.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>Обновление одноранговой информации, связанной с этой подпиской

Ниже приведен пример обновления одноранговой информации.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -Имя соответствует названию ресурса и может быть все, что вы выбираете. Тем не менее, -peerName соответствует названию вашей компании и должен быть как можно ближе к вашему профилю PeeringDB. Обратите внимание, что значение для -peerName поддерживает только символы a-z, A-Я и пространство.

Подписка может иметь несколько ASNs. Обновление вглядывающей информации для каждого ASN. Убедитесь, что "имя" является уникальным для каждого ASN.

Ожидается, что у сверстников будет полный и актуальный профиль на [PeeringDB.](https://www.peeringdb.com) Мы используем эту информацию во время регистрации для проверки данных узла, таких как информация NOC, техническая контактная информация, их присутствие в пиринговых объектах и т.д.

Обратите внимание, что вместо **«subscriptionId»** в вышеуказанном выводе будет отображаться фактический идентификатор подписки.

## <a name="view-status-of-a-peerasn"></a>Просмотр статуса PeerASN

Проверьте состояние проверки ASN с помощью приведенной ниже команды:

```powershell
Get-AzPeerAsn
```

Ниже приведен пример ответа:
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> Подождите, пока состояние валидации повернет "Утверждено", прежде чем отправлять запрос на пиринг. Это утверждение может занять до 12 часов.

## <a name="modify-peerasn"></a>Изменение PeerAsn
Вы можете изменить контактную информацию NOC в любое время.

Ниже приведен пример:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>Удаление PeerAsn
В настоящее время удалять PeerASN не поддерживается. Если вам нужно удалить PeerASN, обратитесь в [запрансь корпорации Майкрософт.](mailto:peering@microsoft.com)

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение прямого пиринга](howto-direct-powershell.md)
* [Преобразование устаревшего прямого пиринга в ресурс Azure](howto-legacy-direct-powershell.md)
* [Создание или изменение пиринга Exchange](howto-exchange-powershell.md)
* [Преобразование устаревшего пиринга через точку обмена в ресурс Azure](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

Для получения дополнительной информации посетите [Интернет пиринг часто задаваемые вопросы](faqs.md)
