---
title: Управление интересами облачного Marketplace | Azure Marketplace и AppSource
description: Общие сведения о различных темах, относящихся к публикации предложений и техническим артефактам в Azure Marketplace и AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: dsindona
ms.openlocfilehash: 94510d02a28e0364f1c715dbcf9ff641fe2b14fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286138"
---
# <a name="lead-management-for-cloud-marketplace"></a>Управление интересами облачного Marketplace


Клиенты являются основой любого успешного бизнеса. В преобразовании сегодняшних приобретений продукта, маркетологи должны сосредоточиться на подключении с клиентами непосредственно и построения отношений. Именно по этой причине формирование списка потенциальных клиентов является важным инструментом цикла продаж. После того, как вы перечислите свои предложения в [портале Cloud Partner](https://cloudpartner.azure.com/), вам будут предоставлены инструменты, которые могут программно получать контактные сведения о клиентах непосредственно после того, как клиент развернул ваш продукт или проявил к нему интерес в Marketplace. 



## <a name="what-are-leads-in-the-marketplace"></a>Что такое интересы в Marketplace?

Интересы поступают от пользователей, которые интересуются или развертывают ваши продукты из Marketplace. Независимо от того, где указан продукт (в Azure Marketplace или в AppSource), вы можете получать интересы от клиентов после его должной настройки из вашей системы CRM в листинг/листинги на портале Cloud Partner 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Как подключить систему CRM к облачному порталу для партнеров

Чтобы начать получать интересы, соединитель управления интересами на портале Cloud Partner разработан таким образом, чтобы его можно было легко подключить с вашей информацией CRM к списку доступных систем CRM. Теперь можно легко использовать интересы, созданные с помощью Marketplace, благодаря простой интеграции с внешней системой.

Ниже приведены пошаговые инструкции по подключению всех возможных назначений интересов:

**Динамика CRM Online** - [Нажмите здесь,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) чтобы получить инструкции о том, как настроить динамику CRM Online для получения приводит.

**Marketo** - [Нажмите здесь,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) чтобы получить инструкции для настройки Marketo свинца Конфигурация, чтобы получить приводит.

**Salesforce** - [Нажмите здесь,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) чтобы получить инструкции для настройки экземпляра Salesforce, чтобы получить интересы.

**Таблица** - Azure[Нажмите здесь,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) чтобы получить инструкции по настройке учетной записи хранения Azure для получения интересов в таблице Azure.

**Https Конечная точка** - [Нажмите здесь,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) чтобы получить инструкции для настройки вашей конечная точка Https, чтобы получить приводит.

После успешной настройки назначения интереса нажмите кнопку "Опубликовать" в вашем предложении, и мы проверим подключение и отправим вам тестовый интерес. Перед тем как использовать предложение в рабочей среде, вы можете проверить подключение интереса, попробовав получить предложение в предварительной среде. Очень важно следить за настройками параметров интересов, чтобы не упустить потенциальных клиентов. Поэтому регулярно обновляйте эти подключения при каждом изменении с вашей стороны.


### <a name="what-are-the-next-steps"></a>Каковы следующие шаги?

После завершения технической настройки нужно включить эти интересы в текущую стратегию продаж и маркетинга, а также в рабочие процессы. Мы стараемся лучше понять общий процесс ваших продаж и за счет тесного сотрудничества с вами можем предоставлять точные сведения о потенциальных клиентах и достаточный объем данных, чтобы помочь вам достичь успеха. Мы приветствуем отзывы касательно оптимизации и улучшения интересов, которые отправляем вместе с дополнительными данными, чтобы помочь таким клиентам. Сообщите нам, если вы заинтересованы в предоставлении обратной связи и предложений, чтобы ваша команда продаж, чтобы быть более успешным с Marketplace Leads.



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>Распространенные ошибки конфигурации интересов во время публикации на портале Cloud Partner 

**Не удалось сохранить привести к динамике CRM. Проверьте настройки учетной записи Dynamics CRM. LastCRMError: Не удается войти в Динамику CRM, LastCRMException:** 

> Если выбрано аутентификацию O365, проверьте допустимость учетной записи пользователя и пароля. Если выбрано AAD, проверьте, соответствуют ли идентификатор клиента, идентификатор приложения и секретный ключ приложения тем, что были настроены на AAD. Следуйте [этим](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) инструкциям. Если имя пользователя/пароль учетной записи является допустимым, убедитесь, что она имеет доступ к Dynamics 365, и ему присвоено лицензию (шаги 11–15, если при использовании пользователя Office используются Azure Active Directory или параметры безопасности). 

 
**Не удалось сохранить привести к динамике CRM. Пользователь не имеет разрешения на атрибут свинцового кода в сущности** 

> В приложения/пользователя нет роли (ролей) безопасности для автора интересов Microsoft Marketplace. Выполните шаги 11–15, если при использовании пользователя Office используются Azure Active Directory или параметры безопасности, [здесь](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics).

**Не удалось сохранить привести к динамике CRM с помощью AAD. Исключение: Арендатор не найден. Этот экземпляр может произойти, если нет активных подписок для арендатора.**  

> Идентификатор каталога, приведенный в разделе управления интересами, является недопустимым каталогом. Пожалуйста, получите идентификатор каталога на основе инструкций на шаг 2 (в azure Active Directory, [отсюда](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Не удалось сохранить привести к динамике CRM. LastCRMError: SecLib::RetrievePrivilegeForUser не удалось - нет ролей, назначенных пользователю.**  

> Решение: назначение роли безопасности для автора интересов Microsoft Marketplace. Следуйте [этим](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) инструкциям в параметрах безопасности 

**Не удалось сохранить привести к динамике CRM с помощью AAD. Исключение:: Приложение с идентификатором в каталоге не найдено** 

> Идентификатор приложения, приведенный в разделе управления интересами, является недопустимым каталогом. Получите идентификатор каталога в соответствии с инструкциями в шаге 8 (в Azure Active Directory, [отсюда](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Не удалось сохранить привести к динамике CRM с помощью AAD. Исключение:: Запрошенный идентификатор арендатора недействителен и не действителен формат внешнего домена** 

> Идентификатор каталога, приведенный в разделе управления интересами, является недопустимым каталогом. Получите идентификатор каталога в соответствии с инструкциями в шаге 2 (в Azure Active Directory, [отсюда](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Не удалось сохранить привести к динамике CRM с помощью AAD. Исключение: Ошибка проверки учетных данных.: Недействительный секрет клиента предоставляется.** 

> Разрешение: Войти на портал Azure, проверьте, соответствует ли ключ приложения тому, что есть в портале облачных партнеров. Получите сгенерированный пароль в соответствии с инструкциями в шаге 10 (в Azure Active Directory, [отсюда](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Не удалось сохранить привести к динамике CRM. LastCRMError: Канал запроса приурочен в ожидании ответа после 00:02:00. Увеличьте значение тайм-аута, передаваемый на запрос, или увеличьте значение Отправки на Связывании. Время, отведенное на эту операцию, возможно, было частью более длительного тайм-аута.**  

> Разрешение: Вопийте на портале Cloud Partner, проверьте детали магазина >> место назначения Lead >> URL, проверьте, является ли это допустимым экземпляром Dynamic CRM

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

**Что такое интересы и почему они важны для меня как издателя на Marketplace?** 

Интересы — это пользователи, которые развертывают ваши продукты из Marketplace. Независимо от того, где содержится ваш продукт (в [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us) или в [AppSource](https://appsource.microsoft.com/)), вы будете иметь возможность получать сведения о потенциальных клиентах, заинтересованных в вашем продукте, если для вашего предложения настроено назначение интереса.  


**Где можно получить помощь про поводу настройки назначения моего интереса?** 

Вы можете найти документацию здесь: [Получить клиентов приводит](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) или представить поддержку билет через aka.ms/marketplacepublishersupport выбрать тип предложения и привести управления. 



**Необходимо ли мне настроить назначение интереса, чтобы опубликовать предложение в Marketplace?**

Да, если вы публикуете приложение SaaS для связи с вами или консультационные услуги.  
 


**Как убедиться в том, что конфигурация интереса правильна?**

После настройки вашего предложения и назначения интереса опубликуйте свое предложение. На этапе проверки интерес Marketplace отправляет тестовый интерес в назначение интереса, настроенное в вашем предложении. 


**Как мне найти тестовый интерес?**


Поиск "MSFT_TEST" в вашем месте назначения, вот пример тестовых данных руководства: 

company = MSFT_TEST_636573304831318844 

country = US (США) 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

encoding = UTF-8 

encoding = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source - MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844 \<Предложение Имя> 

oid = 00Do0000000ZHog 

phone = 1234567890 

title = MSFT_TEST_636573304831318844 

 

**У меня есть живое предложение, но я не вижу никаких зацепок?**

Каждый интерес будет передавать данные в поля выбранного назначения интереса. Интересы будут поступать в следующем формате: **источник — действие|предложение** 

  *Источников:*

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *Действия:*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *Предлагает:*

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *Вот выборочные данные информации о клиентах*

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

Вы можете получить доступ к каплей или таблице с портала Azure, или вы можете скачать и установить [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) бесплатно, чтобы просмотреть таблицы/капли учетной записи хранения Azure. 


**В качестве назначений интересов у меня настроено таблицу Azure. Буду ли я получать уведомления о том, что Marketplace отправил мне новый интерес?** 

Да. Следуйте инструкциям, чтобы задать в [этой ](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) документации таблицу Azure + функцию. 



**В качестве назначений интересов у меня настроено Salesforce. Почему я не могу найти интересы?** 

Проверьте, является ли веб-форма интересов обязательным полем в поле выбора. Если это так, то перейдите через поле к необязательному текстовому полю.  
 

**Существовал вопрос с моим привести назначения, и я пропустил некоторые приводит. Могу ли я отправить их мне по электронной почте?** 

Из-за политики персональных данных мы не можем делиться сведениями об интересах, используя незащищенные сообщения электронной почты. 



**В качестве назначений интересов у меня настроено службу хранилища Azure (BLOB-объект/таблица). Сколько это будет стоить?** 

Небольшое количество данных об интересе (< 1 ГБ для почти всех издателей). Стоимость будет зависеть от количества полученных интересов. Если в течение месяца вы получили 1000 интересов, это будет стоит около 50 центов. 
