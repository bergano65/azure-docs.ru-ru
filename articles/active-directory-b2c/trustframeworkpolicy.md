---
title: TrustFrameworkPolicy — Azure Active Directory B2C | Документация Майкрософт
description: Указание элемента TrustFrameworkPolicy в настраиваемой политике для Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d54d4eccc89313a8e109d1598078cdb1cc5d7a14
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836735"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Пользовательские политики представлены одним или несколькими XML-файлами, которые ссылаются друг на друга в иерархической цепочке. XML-элементы определяют элементы политики, такие как схема утверждений, преобразования утверждений, определения содержимого, поставщики утверждений, технические профили, путь взаимодействия пользователя и шаги оркестрации. Каждый файл политики определяется внутри элемента верхнего уровня **TrustFrameworkPolicy** в файле политики. 

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


Элемент **TrustFrameworkPolicy** содержит следующие атрибуты:

| Атрибут | Обязательно | ОПИСАНИЕ |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Yes | Версия схемы, которая будет использоваться для выполнения политики. Значение должно быть `0.3.0.0`. |
| TenantObjectId | Нет  | Уникальный идентификатор объекта арендатора Azure Active Directory (Azure AD) B2C. |
| TenantId | Yes | Уникальный идентификатор арендатора, к которому относится эта политика. |
| PolicyId | Yes | Уникальный идентификатор политики. Этот идентификатор должен начинаться с префикса *B2C_1A_* |
| PublicPolicyUri | Yes | Универсальный код ресурса (URI) для политики, который является сочетанием идентификатора арендатора и идентификатора политики. |
| DeploymentMode | Нет  | Возможные значения: `Production`, `Debugging` или `Development`. Значение по умолчанию — `Production`. Это свойство используется при отладке политики. Дополнительные сведения см. в статье о [сборе журналов](active-directory-b2c-troubleshoot-custom.md). |
| UserJourneyRecorderEndpoint | Нет  | Конечная точка, которая используется, когда для **DeploymentMode** задано значение `Development`. Значение должно быть равно `urn:journeyrecorder:applicationinsights`. Дополнительные сведения см. в статье о [сборе журналов](active-directory-b2c-troubleshoot-custom.md). |


В следующем примере показано, как указать элемент **TrustFrameworkPolicy**:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

## <a name="inheritance-model"></a>Модель наследования

В пути взаимодействия пользователя обычно используются следующие типы файлов политики:

- **Базовый** файл, содержащий большую часть определений. Чтобы упростить устранение неполадок и долгосрочное обслуживание ваших политик, рекомендуется вносить минимальное число изменений в этот файл.
- Файл **расширений**, содержащий уникальные изменения конфигурации для арендатора. Этот файл политики является производным от базового файла. Он используется для добавления новых функциональных возможностей или переопределения существующих. Например, с его помощью можно реализовать федерацию с новыми поставщиками удостоверений.
- Файл **проверяющей стороны (RP)** — файл с одной задачей, который вызывается непосредственно приложением проверяющей стороны, например веб-приложениями, мобильными или классическими приложениями. Для каждой уникальной задачи, такой как регистрация, вход в систему, сброс пароля или изменение профиля, требуется собственный файл политики проверяющей стороны. Этот файл политики является производным от файла расширений. 

Приложение проверяющей стороны вызывает файл политики проверяющей стороны для выполнения определенной задачи, например, чтобы инициировать процесс входа. Identity Experience Framework в Azure AD B2C добавляет все элементы из базового файла, затем из файла расширений и наконец из файла политики проверяющей стороны, чтобы создать действующую политику. Элементы такого же типа и с тем же именем в файле проверяющей стороны переопределяют те, что находятся в файле расширений, а последние переопределяют значения в базовом файле. На следующей схеме показана связь между файлами политики и приложениями проверяющей стороны.

![Модель наследования](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Модель наследования выглядит следующим образом:

- Родительская и дочерняя политики построены по одинаковой схеме.
- Дочерняя политика на любом уровне может наследоваться от родительской и расширять ее, добавляя новые элементы.
- Ограничение на количество уровней отсутствует.

Дополнительные сведения см. в статье [Azure Active Directory B2C: начало работы с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

## <a name="base-policy"></a>Базовая политика

Чтобы наследовать политику от другой политики, необходимо объявить элемент **BasePolicy** в элементе **TrustFrameworkPolicy** файла политики. Элемент **BasePolicy** представляет собой ссылку на базовую политику, на основе которой создается эта политика.  

В элементе **BasePolicy** содержатся следующие элементы:

| Элемент | Вхождения | ОПИСАНИЕ |
| ------- | ----------- | --------|
| TenantId | 1:1 | Идентификатор арендатора Azure AD B2C. |
| PolicyId | 1:1 | Идентификатор родительской политики. |


В следующем примере показано, как задать базовую политику. Политика **B2C_1A_TrustFrameworkExtensions** является производной от **B2C_1A_TrustFrameworkBase**. 

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

## <a name="policy-execution"></a>Выполнение политики

Приложение проверяющей стороны, например веб-приложение, мобильное или классическое приложение, вызывает [политику проверяющей стороны](relyingparty.md). Файл политики проверяющей стороны выполняет определенную задачу, например вход, сброс пароля или изменение профиля. Политика проверяющей стороны настраивает список утверждений, которые получает приложение проверяющей стороны в составе выданного маркера. Одна и та же политика может использоваться в нескольких приложениях. Все приложения получают один и тот же маркер с утверждениями, а пользователь проходит один и тот же путь взаимодействия. В одном приложении может использоваться несколько политик.

Внутри файла политики проверяющей стороны следует указать элемент **DefaultUserJourney**, который указывает на [UserJourney](userjourneys.md) (путь взаимодействия пользователя). Путь взаимодействия пользователя обычно определяется в базовой политике или политике расширений.

Политика B2C_1A_signup_signin:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase или B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignOrSignIn">
  ...
```

Путь взаимодействия пользователя определяет бизнес-логику этапов, через которые проходит пользователь. Каждый путь взаимодействия пользователя — это набор шагов оркестрации, в рамках которого выполняется ряд последовательных действий с точки зрения проверки подлинности и сбора информации. 

Файл политики **SocialAndLocalAccounts** в [начальном пакете](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom#download-starter-pack-and-modify-policies) содержит пути взаимодействия пользователя SignUpOrSignIn, ProfileEdit и PasswordReset. Можно добавить дополнительные пути для других сценариев, таких как изменение адреса электронной почты, создание и удаление связи с учетной записью социальной сети или сброс пароля. 

Шаги оркестрации могут вызывать [технический профиль](technicalprofiles.md). Технический профиль предоставляет платформу со встроенным механизмом для взаимодействия с различными типами сторон. Например, технический профиль, помимо прочего, может выполнять следующие действия:

- Отображение пользовательского интерфейса.
- Разрешение пользователям выполнять вход с использованием учетной записи социальной сети или корпоративной учетной записи, например Facebook, Майкрософт, Google, Salesforce или любого другого поставщика удостоверений.
- Настройка проверки подлинности по телефону для многофакторной проверки подлинности.
- Чтение данных из хранилище удостоверений Azure AD B2C и запись данных в него.
- Вызов пользовательской службы Restful API.

![Выполнение политики](./media/trustframeworkpolicy/custom-policy-execution.png)

 Элемент **TrustFrameworkPolicy** содержит следующие элементы:

- BasePolicy, как указано выше;
- [BuildingBlocks](buildingblocks.md);
- [ClaimsProviders](claimsproviders.md);
- [UserJourneys](userjourneys.md);
- [RelyingParty](relyingparty.md).

