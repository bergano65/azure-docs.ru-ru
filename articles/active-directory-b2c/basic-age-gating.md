---
title: Включение фильтрации по возрасту в Azure Active Directory B2C | Документация Майкрософт
description: Сведения о том, как определять несовершеннолетних пользователей вашего приложения.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f7eb4d8e784acc659f6661ef6efbdb06816b142c
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064452"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Включение фильтрации по возрасту в Azure Active Directory B2C

>[!IMPORTANT]
>Эта функция предоставляется в общедоступной предварительной версии. Не используйте эту функцию для рабочих приложений.
>

Ограничение возраста в Azure Active Directory B2C (Azure AD B2C) позволяет выявление незначительных версий, которые хотят использовать приложение. Вы можете заблокировать вход в приложение для несовершеннолетних. Пользователи могут вернуться в приложение и указать возрастную группу и состояние родительского согласия. Azure AD B2C может блокировать вход несовершеннолетних, выполняемый без родительского согласия. Также Azure AD B2C можно настроить так, чтобы приложение самостоятельно выбирало действия для несовершеннолетних.

После включения ограничения возраста в [потоке пользователя](active-directory-b2c-reference-policies.md)пользователи получают запрос на то, когда они появились, а также о стране или регионе, в которых они находятся. Пользователь, который еще не вводил эту информацию, должен будет ввести ее для следующего входа. Правила применяются при каждом входе пользователя.

Azure AD B2C использует введенные сведения, чтобы определить, является ли пользователь несовершеннолетним. Затем в учетной записи обновляется поле **ageGroup**. Возможны следующие значения: `null`, `Undefined`, `Minor`, `Adult` или `NotAdult`.  На основе значений **ageGroup** и **consentProvidedForMinor** вычисляется значение для поля **legalAgeGroupClassification**.

При проверке возраста пользователя, пытающегося получить доступ, используются два значения: возраст до наступления совершеннолетия и возраст, для которого требуется согласие родителей. В таблице ниже перечислены правила расчета возраста, которые используются для выявления несовершеннолетних и несовершеннолетних, которым требуется родительское согласие.

| Страна/регион | Название страны или региона | Возраст доступа с согласия | Возраст до наступления совершеннолетия |
| -------------- | ------------------- | ----------------- | --------- |
| Значение по умолчанию | Отсутствуют | Отсутствуют | 18 |
| AE | ОАЭ | Отсутствуют | 21 |
| AT | Австрия | 14 | 18 |
| BE | Бельгия | 14 | 18 |
| BG | Болгария | 16 | 18 |
| BH | Бахрейн | Отсутствуют | 21 |
| CM | Камерун | Отсутствуют | 21 |
| CY | Кипр | 16 | 18 |
| CZ | Чехия | 16 | 18 |
| DE | Германия | 16 | 18 |
| DK | Дания | 16 | 18 |
| EE | Эстония | 16 | 18 |
| EG | Египет | Отсутствуют | 21 |
| ES | Испания | 13 | 18 |
| СВ | Франция | 16 | 18 |
| ГБ | Соединенное Королевство | 13 | 18 |
| GR | Греция | 16 | 18 |
| HR | Хорватия | 16 | 18 |
| HU | Венгрия | 16 | 18 |
| IE | Ирландия | 13 | 18 |
| IT | Италия | 16 | 18 |
| KR | Республика Корея | 14 | 18 |
| LT | Литва | 16 | 18 |
| LU | Люксембург | 16 | 18 |
| LV | Латвия | 16 | 18 |
| MT | Мальта | 16 | 18 |
| Н/Д | Намибия | Отсутствуют | 21 |
| NL | Нидерланды | 16 | 18 |
| PL | Польша | 13 | 18 |
| PT | Португалия | 16 | 18 |
| RO | Румыния | 16 | 18 |
| SE | Швеция | 13 | 18 |
| SG | Сингапур | Отсутствуют | 21 |
| SI | Словения | 16 | 18 |
| SK | Словакия | 16 | 18 |
| TD | Чад | Отсутствуют | 21 |
| ВП | Таиланд | Отсутствуют | 20 |
| TW | Тайвань | Отсутствуют | 20 |
| США | США | 13 | 18 |

## <a name="age-gating-options"></a>Параметры фильтрации по возрасту

### <a name="allowing-minors-without-parental-consent"></a>Разрешение доступа несовершеннолетним пользователям без родительского согласия

Для потоков пользователей, которые позволяют выполнять регистрацию и (или) вход, вы можете разрешить доступ к приложению несовершеннолетним пользователям без родительского согласия. Несовершеннолетним пользователям без родительского согласия будет разрешено выполнять вход или регистрацию обычным образом, а Azure AD B2C будет выдавать маркер идентификации с утверждением **legalAgeGroupClassification**. Это утверждение определяет некоторые действия в интерфейсе, например получение родительского согласия и обновление поля **consentProvidedForMinor**.

### <a name="blocking-minors-without-parental-consent"></a>Блокирование несовершеннолетних пользователей без родительского согласия

Для потоков пользователей, которые позволяют выполнять регистрацию и (или) вход, вы можете заблокировать доступ к приложению для несовершеннолетних пользователей без родительского согласия. Azure AD B2C поддерживает следующие действия для заблокированных пользователей:

- Отправить JSON обратно в приложение. Этот вариант отправляет в приложение ответ о блокировании несовершеннолетнего пользователя.
- Показать страницу ошибки. Пользователю отображается страница с сообщением об отказе в доступе к приложению.

## <a name="set-up-your-tenant-for-age-gating"></a>Настройка проверки возраста в клиенте

Чтобы использовать фильтрацию по возрасту в потоке пользователя, следует настроить дополнительные свойства для клиента.

1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C, выбрав фильтр " **каталог и подписка** " в верхнем меню. Выберите каталог, содержащий ваш клиент.
2. Выберите **Все службы** в левом верхнем углу портала Azure, найдите службу **Azure AD B2C** и выберите ее.
3. Выберите **Properties** (Свойства) для клиента в меню слева.
2. В разделе **Age gating** (Фильтрация по возрасту) щелкните **Configure** (Настроить).
3. После завершения операции в клиенте будет включена фильтрация по возрасту.

## <a name="enable-age-gating-in-your-user-flow"></a>Включение фильтрации по возрасту в потоке пользователя

Настроив в клиенте фильтрацию по возрасту, вы сможете применять эту функцию в тех [потоках пользователей](user-flow-versions.md), где она включена. Чтобы включить фильтрацию по возрасту, сделайте следующее:

1. Создайте поток пользователя с включенной фильтрацией по возрасту.
2. Создав поток пользователя, выберите в меню пункт **Properties** (Свойства).
3. В разделе **Age gating** (Фильтрация по возрасту) выберите **Enabled** (Включено).
4. Теперь можно решить, какие действия применять для пользователей, определенных как несовершеннолетние. Для параметра **Sign-up or sign-in** (Регистрация или вход) выберите значение `Allow minors to access your application` или `Block minors from accessing your application`. Если выбрана блокировка несовершеннолетних, выберите `Send a JSON back to the application` или `Show an error message`.




