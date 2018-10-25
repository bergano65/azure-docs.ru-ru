---
title: Часто задаваемые вопросы об элементе "Модуль IoT Edge" в Marketplace | Документация Майкрософт
description: Часто задаваемые вопросы об элементе "Модуль IoT Edge" в Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2c2e7729eb70a5dd37dc4df10605eec9429e1043
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807464"
---
# <a name="iot-edge-module-marketplace-frequently-asked-questions"></a>Часто задаваемые вопросы об элементе "Модуль IoT Edge" в Marketplace


## <a name="what-is-the-edge-module-marketplace"></a>Что такое элемент "Модуль IoT Edge" в Marketplace?


Элемент "Модуль IoT Edge" в Marketplace представляет собой список *сертифицированных* готовых модулей Edge, которые *обнаруживаются* через Azure Marketplace.

![Модули IoT Edge](./media/cloud-partner-portal-iot-edge-module-faq/iot-edge-modules.png)

## <a name="where-will-edge-modules-be-visible"></a>Где будут видимы модули Edge? 


В [разделе Marketplace на портале Azure](https://ms.portal.azure.com/) (нужно пройти аутентификацию) в категории "Интернет вещей" под названием "Модуль IoT Edge".

На [веб-сайте Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1) (анонимный интерфейс) в категории "Интернет вещей" под названием "Модуль IoT Edge".

## <a name="is-it-open-to-partners"></a>Открыт ли он для партнеров?


Пока нет. В настоящее время только авторизованные Майкрософт модули публикуются в разделе IoT Edge на Marketplace. 

## <a name="why-should-partners-publish-their-iot-edge-modules"></a>Почему партнерам следует публиковать свои модули IoT Edge?


-   Чтобы повысить видимость продукта путем добавления этого канала выхода на рынок и демонстрации своих решений.

-   Чтобы получить больше потенциальных клиентов. Являясь частью Azure Marketplace, они могут получить сведения о том, кто заинтересован в их решении.

-   Чтобы одним из первых использовать дополнительные возможности монетизации.

## <a name="what-is-the-onboarding-process"></a>Каков процесс подключения?


Хотя он еще не открыт, процесс подключения будет выполняться с помощью Azure Marketplace. Дополнительные сведения. см. в [руководстве по публикации в Azure Marketplace и AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide). Обратитесь к типу приложения "Транзакция" для контейнеров. 

Сначала партнерам необходимо стать издателем Azure Marketplace. Затем они будут иметь возможность подключить свои модули Edge через [Портал Cloud Partner](./cloud-partner-portal-getting-started-with-the-cloud-partner-portal.md).

## <a name="are-there-any-monetization-capabilities"></a>Существуют ли какие-либо возможности монетизации?


В настоящее время готовых возможностей не существует. Наша первая цель — разместить в Marketplace модули Edge с возможностью использования *бесплатных* и *собственных лицензий*. Мы добавим больше возможностей монетизации, такие как модель выставление счетов за потребление.

## <a name="what-is-bring-your-own-license-byol"></a>Что такое использование собственной лицензии (BYOL)?


Официальное определение см. в статье [Политики участия в Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

- *Клиенты получают право на доступ к предложению за пределами Azure Marketplace и его использование, с них не взимается плата Azure Marketplace за использование предложения в Azure Marketplace.*

Партнеры могут лицензировать свое программное обеспечение и получать от него прибыль.

## <a name="can-partners-publish-a-freemium-module"></a>Могут ли партнеры опубликовать условно-бесплатный модуль?


Да, условно-бесплатные модули, то есть доступные бесплатно с некоторыми ограничениями, будут рассматриваться, как любая другая публикация.

## <a name="is-intellectual-property-protected"></a>Защищается ли интеллектуальная собственность?


Модуль Edge — это совместимый контейнер Docker. Партнеры могут защищать свою интеллектуальную собственность, упакованную в распределенные контейнеры.

## <a name="where-will-the-modules-be-hosted"></a>Где будут размещаться модули?


Модули IoT Edge будут размещены в реестре контейнеров, который принадлежит корпорации Майкрософт и к которому можно отправлять анонимные запросы, как к Центру Docker.

## <a name="what-are-the-integration-plans-between-the-azure-marketplace-and-the-azure-iot-tools"></a>Каковы планы интеграции между Azure Marketplace и средствами Интернета вещей Azure?

Мы создадим более тесную интеграцию между Azure Marketplace и средствами Интернета вещей Azure. Например, мы хотим реализовать возможность просмотра элемента "Модуль IoT Edge" в Marketplace непосредственно с портала Центра Интернета вещей или непосредственно из Visual Studio Code.

## <a name="which-operating-system-or-architecture-should-my-container-support"></a>Какую операционную систему или архитектуру должен поддерживать мой контейнер?

Модули IoT Edge должны поддерживать одну операционную систему или матрицу архитектуры в качестве IoT Edge в рабочей среде. Этот список можно найти в статье [Поддержка Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support). Например, в настоящее время модуль должен поддерживать Linux x64 и Linux ARM32.

## <a name="are-there-any-other-certification-constraints-to-be-aware-of"></a>Существуют ли какие-либо другие ограничения сертификации, которые следует учитывать?

Мы по-прежнему работаем над точным набором ограничений сертификации. Ниже приведены наши принципы работы:

-   предпочтение качества количеству;

-   контейнеры определенных модулей IoT Edge (а не случайные);

-   производственный уровень;

-   развертывание одним щелчком (по крайней мере с предоставленным набором значений конфигурации по умолчанию).

## <a name="any-other-questions"></a>Есть другие вопросы?


Обратитесь в [службу поддержки по Azure IoT Edge](mailto:azureiotedgeonboarding@service.microsoft.com).
