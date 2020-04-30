---
title: Использование образов клиентов Windows в Azure
description: Узнайте, как использовать преимущества подписки Visual Studio для развертывания Windows 7, Windows 8 или Windows 10 в Azure в сценариях разработки и тестирования.
author: cynthn
ms.subservice: imaging
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 51e4862fe55428f112841ef7176cdb916653de0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82083280"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Использование клиента Windows в Azure для сценариев разработки и тестирования
В сценариях разработки и тестирования Azure можно использовать Windows 7, Windows 8 или Windows 10 Корпоративная (x64) при условии, что у вас есть соответствующая подписка Visual Studio (прежнее название — MSDN). В этой статье описываются требования к доступности при запуске Windows 7, Windows 8.1 и Windows 10 Корпоративная в Azure и использовании образов из коллекции Azure.

![Сведения об образах на портале Azure](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Дополнительные сведения об образе Windows 10 Pro и Windows 10 Pro N в коллекции Azure см. в статье [развертывание Windows 10 в Azure с использованием многоклиентской службы siteing Rights](windows-desktop-multitenant-hosting-deployment.md)
>![Pro с помощью портал Azure](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Доступность в зависимости от подписки
Активные подписчики Visual Studio (пользователи, которые приобрели лицензию на подписку Visual Studio) могут использовать клиент Windows в целях разработки и тестирования. Вы можете использовать клиент Windows на собственном оборудовании и виртуальных машинах Azure, работающих в любом типе подписки Azure. Клиент Windows не может быть развернут или использоваться в Azure в обычной рабочей среде, а также недоступен для пользователей, не являющихся активными подписчиками Visual Studio.

Для вашего удобства доступны несколько образов Windows 10 из коллекции Azure, которые можно использовать для разработки и тестирования. См. раздел [Доступные предложения](#eligible-offers). Подписчики Visual Studio с предложением любого типа также смогут [правильно подготавливать и создавать](prepare-for-upload-vhd-image.md) 64-разрядные образы Windows 7, Windows 8 или Windows 10, а затем [отправлять их в Azure](upload-generalized-managed.md). Они также могут использоваться только активными подписчиками Visual Studio и только в целях разработки и тестирования.

## <a name="eligible-offers"></a>Доступные предложения
В следующей таблице перечислены идентификаторы предложений, которые доступны для развертывания Windows 10 с помощью коллекции Azure. Образы Windows 10 отображаются только для указанных ниже предложений. Подписчики Visual Studio, которым необходимо запустить клиент Windows с помощью другого типа предложения, должны [правильно подготовить и создать](prepare-for-upload-vhd-image.md) 64-разрядный образ Windows 7, Windows 8 или Windows 10, а затем [передать его в Azure](upload-generalized-managed.md).

| Название предложения | Номер предложения | Доступные образы клиента |
|:--- |:---:|:---:|
| [Разработка и тестирование с оплатой по мере использования](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |быть под управлением ОС Windows 10; |
| [Подписчики Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |быть под управлением ОС Windows 10; |
| [Подписчики Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |быть под управлением ОС Windows 10; |
| [Подписчики Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |быть под управлением ОС Windows 10; |
| [Visual Studio Premium с подпиской MSDN (преимущество)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |быть под управлением ОС Windows 10; |
| [Подписчики Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |быть под управлением ОС Windows 10; |
| [Подписчики Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |быть под управлением ОС Windows 10; |
| [Enterprise — разработка и тестирование](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |быть под управлением ОС Windows 10; |

## <a name="check-your-azure-subscription"></a>Проверка подписки Azure
Если вы не знаете идентификатор своего предложения, его можно найти на портале Azure одним из двух способов:  

- В окне *Подписки*:

  ![Сведения об идентификаторе предложения на портале Azure](./media/client-images/offer-id-azure-portal.png) 

- Или щелкните **Выставление счетов** и выберите свой идентификатор подписки. Идентификатор предложения отобразится в окне *Выставление счетов*.

На портале учетных записей Azure просмотреть идентификатор предложения можно на [вкладке "Подписки"](https://account.windowsazure.com/Subscriptions) :

![Сведения об идентификаторе предложения на портале учетных записей Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Дальнейшие шаги
Теперь вы можете развернуть виртуальные машины с помощью [PowerShell](quick-create-powershell.md), [шаблонов Resource Manager](ps-template.md) или [Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

