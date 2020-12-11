---
title: Руководство по настройке Azure Active Directory B2C с помощью Zscaler
titleSuffix: Azure AD B2C
description: Узнайте, как интегрировать проверку подлинности Azure AD B2C с Zscaler
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: ff51c2a71dfcaec580733a92e265628ac816e229
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97096181"
---
# <a name="tutorial-to-configure-zscaler-private-access-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Руководство по настройке Zscaler закрытого доступа с помощью Azure Active Directory B2C для безопасного гибридного доступа

В этом руководстве описано, как интегрировать проверку подлинности Azure AD B2C с [Zscaler Private Access (ZPA)](https://www.zscaler.com/products/zscaler-private-access). ZPA обеспечивает безопасный доступ к частным приложениям и ресурсам на основе политик без затрат, проблем или угроз безопасности виртуальной частной сети (VPN). Защищенное предложение гибридного доступа Zscaler позволяет получать нулевую атаку для приложений, ориентированных на потребителей, в сочетании с Azure AD B2C.

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, вам потребуется:

- Подписка Azure. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).

- [Клиент Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) , связанный с вашей подпиской Azure.

- [Подписка ZPA](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)

## <a name="scenario-description"></a>Описание сценария

Интеграция ZPA включает следующие компоненты:

- Azure AD B2C — поставщик удостоверений (IdP), отвечающий за проверку учетных данных пользователя. Он также отвечает за регистрацию нового пользователя.

- ZPA — служба, ответственная за защиту веб-приложения путем принудительного [доступа с нулевым доверием](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.).

- Веб-приложение — размещает службу, к которой пытается получить доступ пользователь.

На следующей схеме показано, как ZPA интегрируется с Azure AD B2C.

![На рисунке показана схема архитектуры Zscaler](media/partner-zscaler/zscaler-architecture-diagram.png)

|Шаг | Описание |
|:-----| :-----------|
| 1. | Пользователь приступит к ZPA пользовательскому порталу или приложению доступа к браузеру ZPA.
| 2. | Чтобы решить, следует ли разрешить пользователю доступ к веб-приложению, ZPA требует сведений о контексте пользователя. Для проверки подлинности пользователя ZPA выполняет перенаправление SAML на страницу входа Azure AD B2C.  
| 3. | Пользователь поступает на странице входа в B2C Azure AB. Если это новый пользователь, пользователь регистрируется для создания новой учетной записи. Существующий пользователь будет входить с использованием существующих учетных данных. Azure AD B2C проверяет удостоверение пользователя.
| 4. | После успешной проверки подлинности Azure AD B2C перенаправляет пользователя обратно в ZPA вместе с утверждением SAML. ZPA проверяет SAML assertion и задает контекст пользователя.
| 5. | ZPA оценивает политики доступа для пользователя. Если пользователю разрешен доступ к веб-приложению, подключение может проходить через.

## <a name="onboard-to-zpa"></a>Подключение к ZPA

В этом учебнике предполагается, что у вас уже есть рабочая настройка ZPA. Если вы приступите к работе с ZPA, см. [Пошаговое руководство по настройке ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-with-azure-ad-b2c"></a>Интеграция с Azure AD B2C

### <a name="part-1---configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Часть 1. Настройка Azure AD B2C в качестве IdP в ZPA

Настройка Azure AD B2C [в качестве поставщика удостоверений (IDP) в ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign):

1. Войдите на [портал администрирования ZPA](https://admin.private.zscaler.com) .

2. Перейдите в раздел **Administration**  >  **IDP Configuration** (администрирование конфигурации).

3. Выберите **Add IDP Configuration (Добавить конфигурацию конфигурации** ).

4. Для **получения сведений о 1 IDP** введите следующее:

   а. **Имя**: Azure AD B2C

   b. **Единый вход**: выбор пользователя

   c. **Домены**: выберите домены проверки подлинности, которые необходимо связать с этим IDP, а затем нажмите кнопку **Готово** .

   ![На рисунке показаны сведения о IDP](media/partner-zscaler/add-idp-configuration.png)

5. Щелкните **Далее**.

6. Для **2 МЕТАДАННЫХ SP**:

   а. Скопируйте URL-адрес поставщика услуг и запишите его для последующего использования.

   b. Скопируйте идентификатор сущности поставщика услуг и запишите его для последующего использования.

   ![На рисунке показаны сведения о метаданных SP](media/partner-zscaler/sp-metadata.png)

7. Выбор **приостановки**

Оставшаяся часть конфигурации IdP будет возобновлена после настройки Azure AD B2C.

### <a name="part-2---configure-custom-policy-in-azure-ad-b2c"></a>Часть 2. Настройка настраиваемой политики в Azure AD B2C

>[!Note]
>Этот шаг необходим только в том случае, если у вас еще нет пользовательских политик. Если у вас уже есть одна или несколько настраиваемых политик, этот шаг можно пропустить.

В статье Приступая к [работе с пользовательскими политиками в Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) приводятся инструкции по настройке пользовательских политик в клиенте Azure AD B2C.

### <a name="part-3---register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Часть 3. Регистрация ZPA в качестве приложения SAML в Azure AD B2C

В статье [Регистрация приложения SAML в Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers) приводятся инструкции по настройке приложения saml в Azure AD B2C. На [шаге 3,2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#32-upload-and-test-your-policy-metadata)вы получите URL-адрес метаданных SAML IDP, используемый Azure AD B2C. Он понадобится вам для последующего использования.

Следуйте инструкциям на [шаге 4,2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#42-update-the-app-manifest). На шаге 4,2 инструкции требуется обновить манифест приложения. Обновите свойства следующим образом:

- **identifierUris**: используйте идентификатор сущности поставщика услуг, указанный в **части 1, шаг 6a**.

- **самлметадатаурл**: пропустите это свойство, так как ZPA не размещает URL-адрес метаданных SAML.

- **реплюрлсвистипе**: используйте URL-адрес поставщика службы, указанный в **части 1, шаг 6B**.

- **логаутурл**: пропустите это свойство, так как ZPA не поддерживает URL-адрес выхода.

Остальные шаги не связаны с этим руководством.

### <a name="part-4---extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Часть 4. Извлечение метаданных IdP SAML из Azure AD B2C

На предыдущем шаге необходимо получить URL-адрес метаданных SAML в следующем формате:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

где `<tenant-name>` — имя клиента Azure AD B2C, а `<policy-name>` — имя настраиваемой политики SAML, созданной на предыдущем шаге.

Например https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata.

Откройте веб-браузер и перейдите по URL-адресу метаданных SAML. При загрузке страницы щелкните правой кнопкой мыши в любом месте страницы. Выберите **сохранить страницу как** и сохраните файл на компьютере. Он будет использоваться в следующей части.

### <a name="part-5---complete-idp-configuration-on-zpa"></a>Часть 5. Завершение настройки IdP на ZPA

Завершите [настройку IDP на портале администрирования ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign) , частично настроенном в части 1:

1. Войдите на [портал администрирования ZPA](https://admin.private.zscaler.com) .

2. Перейдите в раздел **Administration**  >  **IDP Configuration**(администрирование конфигурации).

3. Выберите IdP, который был настроен в части 1, и нажмите кнопку **возобновить**.

4. Для **3. Create IDP**

   а. Перейдите к **файлу метаданных IDP**.  >  **выберите файл** и отправьте файл метаданных, сохраненный в части 4.

   b. Убедитесь, **что** конфигурация IDP **включена**.

   В. Нажмите кнопку **Сохранить**.

      ![На рисунке показана информация о создании IDP](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>Тестирование решения

Перейдите на пользовательский портал ZPA или приложение для доступа к браузеру и протестируйте процесс регистрации или входа. Это должно привести к успешной проверке подлинности SAML.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях:

- [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)

- [Регистрация приложения SAML в Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers)

- [Пошаговое руководств по настройке ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)

- [Настройка IdP для единого входа](https://help.zscaler.com/zpa/configuring-idp-single-sign)
