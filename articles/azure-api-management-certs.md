---
title: Передача сертификата управления службами Azure | Документация Майкрософт
description: Узнайте, как передать сертификат управления службами на портал Azure.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: jeconnoc
ms.openlocfilehash: 014a26c2500959502eeb1c50d3f311584c1ad84e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798627"
---
# <a name="upload-an-azure-service-management-certificate"></a>Передача сертификата управления службами Azure
Сертификаты управления позволяют выполнять аутентификацию с помощью классической модели развертывания Azure. Многие программы и инструменты (например, Visual Studio или пакет SDK Azure) будут использовать эти сертификаты для автоматизации настройки и развертывания разных служб Azure. 

> [!WARNING]
> Будьте осторожны! Эти типы сертификатов позволяют каждому, кто прошел проверку подлинности, управлять подпиской, с которой они связаны.
>
>

Дополнительные сведения о сертификатах Azure (включая сведения о создании самозаверяющего сертификата) см. в разделе [Что такое сертификаты управления](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Для автоматизации проверки подлинности клиентского кода можно также использовать службу [Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

**Примечание.** Будьте соадминистратора для подписки для выполнения любых операций с сертификатами управления. [Узнайте](https://go.microsoft.com/fwlink/?linkid=849300), как добавить или удалить соадминистратора на новом портале Azure. 

## <a name="upload-a-management-certificate"></a>Отправка сертификата управления
Создав сертификат управления (CER-файл только с открытым ключом), передайте его на портал. Когда сертификат доступен на портале, любой пользователь с соответствующим сертификатом (закрытым ключом) сможет подключаться через API управления и работать с ресурсами связанной подписки.

1. Войдите на [портал Azure](https://portal.azure.com).
2. Щелкните **Все службы** внизу списка служб Azure и выберите **Подписки** в группе служб _Общие_.

    ![Меню "Подписка"](./media/azure-api-management-certs/subscriptions_menu.png)

3. Обязательно выберите именно ту подписку, с которой необходимо связать сертификат.     
4. Выбрав правильную подписку, щелкните **Сертификаты управления** в группе _Параметры_.

    ![Параметры](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Нажмите кнопку **Отправить** .

    ![Кнопка "Отправить" на странице сертификатов](./media/azure-api-management-certs/certificates_page.png)
6. Укажите сведения в диалоговом окне и нажмите кнопку **Отправить**.

    ![Параметры](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Дальнейшие действия
Связав сертификат управления с подпиской и установив соответствующий сертификат локально, вы можете программно подключаться к [REST API классической модели управления](/azure/#pivot=sdkstools) и автоматизировать различные ресурсы Azure, связанные с этой же подпиской.
