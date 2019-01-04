---
title: Управление интересами облачного Marketplace | Azure Marketplace и AppSource
description: Общие сведения о различных темах, относящихся к публикации предложений и техническим артефактам в Azure Marketplace и AppSource
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: yijenj
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: Marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/05/2018
ms.author: yijenj
ms.openlocfilehash: 8bb964308e11d238def4eed3f194ffc9ef8ab086
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840169"
---
# <a name="lead-management-for-cloud-marketplace"></a>Управление интересами облачного Marketplace


Клиенты являются основой любого успешного бизнеса. В условиях современного рынка поставщикам продуктов и услуг необходимо сосредоточиться на непосредственном общении с клиентами и выстраивании отношений. Именно по этой причине формирование списка потенциальных клиентов является важным инструментом цикла продаж. После того, как вы перечислите свои предложения в [портале Cloud Partner](https://cloudpartner.azure.com/), вам будут предоставлены инструменты, которые могут программно получать контактные сведения о клиентах непосредственно после того, как клиент развернул ваш продукт или проявил к нему интерес в Marketplace. 



## <a name="what-are-leads-in-the-marketplace"></a>Что такое интересы в Marketplace?

Интересы поступают от пользователей, которые интересуются или развертывают ваши продукты из Marketplace. Независимо от того, где указан продукт (в Azure Marketplace или в AppSource), вы можете получать интересы от клиентов после его должной настройки из вашей системы CRM в листинг/листинги на портале Cloud Partner 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Как подключить систему CRM к облачному порталу для партнеров

Чтобы начать получать интересы, соединитель управления интересами на портале Cloud Partner разработан таким образом, чтобы его можно было легко подключить с вашей информацией CRM к списку доступных систем CRM. Теперь можно легко использовать интересы, созданные с помощью Marketplace, благодаря простой интеграции с внешней системой.

Ниже приведены пошаговые инструкции по подключению всех возможных назначений интересов:

**Dynamics CRM Online** - [Щелкните здесь](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics), чтобы получить инструкции по настройке Dynamics CRM Online для получения интересов.

**Marketo** - [Щелкните здесь](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo), чтобы получить инструкции по настройке Marketo для получения интересов.

**SalesForce** - [Щелкните здесь](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce), чтобы получить инструкции по настройке экземпляра Salesforce для получения интересов.

**Таблица Azure** – [Щелкните здесь](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table), чтобы получить инструкции по настройке учетной записи хранения Azure для получения интересов в таблице Azure.

**Конечная точка HTTP** – [Щелкните здесь](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https), чтобы получить инструкции по настройке конечной точки HTTP для получения сведений о потенциальных клиентах.

После успешной настройки назначения интереса нажмите кнопку "Опубликовать" в вашем предложении, и мы проверим подключение и отправим вам тестовый интерес. Перед тем как использовать предложение в рабочей среде, вы можете проверить подключение интереса, попробовав получить предложение в предварительной среде. Очень важно следить за настройками параметров интересов, чтобы не упустить потенциальных клиентов. Поэтому регулярно обновляйте эти подключения при каждом изменении с вашей стороны.


### <a name="what-are-the-next-steps"></a>Каковы следующие шаги?

После завершения технической настройки нужно включить эти интересы в текущую стратегию продаж и маркетинга, а также в рабочие процессы. Мы стараемся лучше понять общий процесс ваших продаж и за счет тесного сотрудничества с вами можем предоставлять точные сведения о потенциальных клиентах и достаточный объем данных, чтобы помочь вам достичь успеха. Мы приветствуем отзывы касательно оптимизации и улучшения интересов, которые отправляем вместе с дополнительными данными, чтобы помочь таким клиентам. Сообщите нам, если вы хотите оставить отзывы и предложения для более эффективного использования интересов Marketplace в вашем отделе продаж.



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>Распространенные ошибки конфигурации интересов во время публикации на портале Cloud Partner 

**Не удалось сохранить интерес в Dynamics CRM. Проверьте параметры учетной записи Dynamics CRM. LastCRMError: Unable to Login to Dynamics CRM, LastCRMException:** 

> Если выбрано аутентификацию O365, проверьте допустимость учетной записи пользователя и пароля. Если выбрано AAD, проверьте, соответствуют ли идентификатор клиента, идентификатор приложения и секретный ключ приложения тем, что были настроены на AAD. Следуйте [этим](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) инструкциям. Если имя пользователя/пароль учетной записи является допустимым, убедитесь, что она имеет доступ к Dynamics 365, и ему присвоено лицензию (шаги 11–15, если при использовании пользователя Office используются Azure Active Directory или параметры безопасности). 

 
**Не удалось сохранить интерес в Dynamics CRM. У пользователя нет разрешений на создание атрибута leadourcecode в объекте интереса**  

> В приложения/пользователя нет роли (ролей) безопасности для автора интересов Microsoft Marketplace. Выполните шаги 11–15, если при использовании пользователя Office используются Azure Active Directory или параметры безопасности, [здесь](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics).

**Не удалось сохранить интерес в Dynamics CRM, используя AAD. Exception:: Tenant not found. Этот экземпляр может произойти, если у клиента отсутствуют активные подписки.**  

> Идентификатор каталога, приведенный в разделе управления интересами, является недопустимым каталогом. Получите идентификатор каталога в соответствии с инструкциями в шаге 2 (в Azure Active Directory, [отсюда](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Не удалось сохранить интерес в Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser failed - no roles are assigned to user.**  

> Способы устранения: назначьте роль безопасности автору интересов Microsoft Marketplace. Следуйте [этим](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) инструкциям в параметрах безопасности 

**Не удалось сохранить интерес в Dynamics CRM, используя AAD. Exception:: Application with identifier was not found in the directory** 

> Идентификатор приложения, приведенный в разделе управления интересами, является недопустимым каталогом. Получите идентификатор каталога в соответствии с инструкциями в шаге 8 (в Azure Active Directory, [отсюда](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Не удалось сохранить интерес в Dynamics CRM, используя AAD. Exception:: Requested tenant identifier is not valid and not valid external domain format** 

> Идентификатор каталога, приведенный в разделе управления интересами, является недопустимым каталогом. Получите идентификатор каталога в соответствии с инструкциями в шаге 2 (в Azure Active Directory, [отсюда](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Не удалось сохранить интерес в Dynamics CRM, используя AAD. Exception:: Error validating credentials.: Invalid client secret is provided.** 

> Способы устранения: войдите на портал Azure и проверьте, соответствует ли ключ приложения значениям на портале Cloud Partner. Получите сгенерированный пароль в соответствии с инструкциями в шаге 10 (в Azure Active Directory, [отсюда](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Не удалось сохранить интерес в Dynamics CRM. LastCRMError: The request channel timed out while waiting for a reply after 00:02:00. Увеличьте значение времени ожидания, которое передается во время вызова запроса, или значение SendTimeout в привязке. Время, назначенное для выполнения этой операции, может быть составной частью более длинного времени ожидания.**  

> Способы устранения: войдите на портал Cloud Partner и проверьте подробные сведения об онлайн-магазинах, назначения интереса, URL-адрес, а затем проверьте, является ли экземпляр Dynamic CRM допустимым.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

**Что такое интересы и почему они важны для меня как издателя на Marketplace?** 

Интересы — это пользователи, которые развертывают ваши продукты из Marketplace. Независимо от того, где содержится ваш продукт (в [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us) или в [AppSource](https://appsource.microsoft.com/)), вы будете иметь возможность получать сведения о потенциальных клиентах, заинтересованных в вашем продукте, если для вашего предложения настроено назначение интереса.  


**Где можно получить помощь про поводу настройки назначения моего интереса?** 

Документацию см. здесь: [получение сведений об интересах клиента](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads). Также вы можете отправить запрос в службу поддержки через тип aka.ms/marketplacepublishersupport, выбрав тип предложения и управление интересами. 



**Необходимо ли мне настроить назначение интереса, чтобы опубликовать предложение в Marketplace?**

Да, если вы публикуете приложение SaaS для связи с вами или консультационные услуги.  
 


**Как убедиться в том, что конфигурация интереса правильна?**

После настройки вашего предложения и назначения интереса опубликуйте свое предложение. На этапе проверки интерес Marketplace отправляет тестовый интерес в назначение интереса, настроенное в вашем предложении. 


**Как мне найти тестовый интерес?**


Найдите в назначении интереса "MSFT_TEST". Ниже приведен пример данных тестового интереса: 

company = MSFT_TEST_636573304831318844 

country = US (США) 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

encoding = UTF-8 

encoding = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name> 

oid = 00Do0000000ZHog 

phone = 1234567890 

title = MSFT_TEST_636573304831318844 

 

**У меня есть действующее предложение, но я не вижу интересов?**

Каждый интерес будет передавать данные в поля выбранного назначения интереса. Интересы будут поступать в следующем формате: **источник — действие|предложение**. 

  *Источники:*

    “AzureMarketplace”, 
    “AzurePortal”, 
    “TestDrive”,  
    “SPZA” (acronym for AppSource) 

  *Действия:*

    “INS” – Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    “PLT” – Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    “DNC” – Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    “Create” – This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

    “StartTestDrive” – This is for Test Drives only and is whenever a customer starts their test drive. 


  *Предложения:*

    “checkpoint.check-point-r77-10sg-byol”, 
    “bitnami.openedxcypress”, 
    “docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a” 

 

  *Ниже приведен пример данных информации о клиенте*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Дополнительные сведения см. в [Lead Info](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) (Информация по поводу интересов). 


**В качестве назначений интересов у меня настроено BLOB-объект Azure. Почему интерес не отображается?** 

Интерес записывается только в том случае, если в качестве назначения интереса вы выбрали BLOB-объект Azure. Перейдите в таблицу Azure для получения реального времени интереса 


**Мне пришло электронное письмо от Marketplace. Почему мне не удается найти интерес в моей CRM?**  

Вполне возможно, что домен электронной почты пользователя — из .edu. В целях конфиденциальности не передавайте персональные данные из домена .edu. Отправьте запрос в службу поддержки через aka.ms/marketplacepublishersupport 


 **В качестве назначений интересов у меня настроено таблицу Azure/BLOB-объект Azure. Как я могу просмотреть интересы?** 

Вы можете получить доступ к BLOB-объекту или таблице с портала Azure или можете бесплатно загрузить и установить [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/) для просмотра таблиц/BLOB-объектов вашей учетной записи службы хранилища Azure. 


**В качестве назначений интересов у меня настроено таблицу Azure. Буду ли я получать уведомления о том, что Marketplace отправил мне новый интерес?** 

Да. Следуйте инструкциям, чтобы задать в [этой ](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) документации таблицу Azure + функцию. 



**В качестве назначений интересов у меня настроено Salesforce. Почему я не могу найти интересы?** 

Проверьте, является ли веб-форма интересов обязательным полем в поле выбора. Если это так, то перейдите через поле к необязательному текстовому полю.  
 

**Возникла проблема с моим назначением интереса, и у меня пропущено несколько интересов. Можно чтобы их отправили мне по электронной почте?** 

Из-за политики персональных данных мы не можем делиться сведениями об интересах, используя незащищенные сообщения электронной почты. 



**В качестве назначений интересов у меня настроено службу хранилища Azure (BLOB-объект/таблица). Сколько это будет стоить?** 

Небольшое количество данных об интересе (< 1 ГБ для почти всех издателей). Стоимость будет зависеть от количества полученных интересов. Если в течение месяца вы получили 1000 интересов, это будет стоит около 50 центов. 
