---
title: Azure Active Directory B2C. Идентификаторы строк локализации | Документация Майкрософт
description: Сведения об указании идентификаторов для определения содержимого с идентификатором api.signuporsignin в настраиваемой политике в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/02/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: aa90775df4462328ed7c39e70c8dd1989248e308
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900520"
---
# <a name="localization-string-ids"></a>Идентификаторы строк локализации

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Элемент **Localization** позволяет поддерживать несколько языковых стандартов или языков в политике для путей взаимодействия пользователя. В этой статье предоставлен список идентификаторов локализации, которые можно использовать в политике. Чтобы ознакомиться с локализацией пользовательского интерфейса, обратитесь к статье [Локализация](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Элементы страниц регистрации или входа

Следующие идентификаторы используются для определения содержимого с идентификатором `api.signuporsignin`.

| ID | Значение по умолчанию |
| -- | ------------- |
| **local_intro_email** | Войти, используя имеющуюся учетную запись |
| **logonIdentifier_email** | Электронная почта |
| **requiredField_email** | Введите свой адрес электронной почты |
| **invalid_email** | Введите действительный адрес электронной почты |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&'' *+/=?^_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)* $ |
| **local_intro_username** | Войти, используя свое имя пользователя |
| **logonIdentifier_username** | Имя пользователя |
| **requiredField_username** | Введите свое имя пользователя |
| **password** | Пароль |
| **requiredField_password** | Введите пароль |
| **invalid_password** | Введенный пароль не соответствует ожидаемому формату. |
| **forgotpassword_link** | Забыли пароль? |
| **createaccount_intro** | Нет учетной записи? |
| **createaccount_link** | Зарегистрируйтесь сейчас |
| **divider_title** | OR |
| **cancel_message** | Пользователь забыл свой пароль |
| **button_signin** | Вход |
| **social_intro** | Войти, используя учетную запись социальной сети |
  **remember_me** |Оставаться в системе|
| **unknown_error** | Возникли проблемы со входом в систему. Повторите попытку позже. |

В следующем примере показано использование некоторых элементов пользовательского интерфейса на странице регистрации или входа в систему:

![Элементы пользовательского интерфейса страницы регистрации или входа](./media/localization-string-ids/localization-susi.png)

Идентификаторы поставщиков удостоверений настраиваются в элементе пути взаимодействия пользователя **ClaimsExchange**. Для локализации названия поставщика удостоверений необходимо присвоить **ElementType** значение `ClaimsProvider`, а для **StringId** присваивается идентификатор `ClaimsExchange`.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

В следующем примере показана локализация поставщика удостоверений Facebook на арабский язык:

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>Сообщения об ошибках при входе или регистрации

| ID | Значение по умолчанию |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Неправильный пароль. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Нам не удается найти вашу учетную запись. |
| **UserMessageIfOldPasswordUsed** | Вероятно, вы используете старый пароль. |
| **DefaultMessage** | Недопустимое имя пользователя или пароль. |
| **UserMessageIfUserAccountDisabled** | Ваша учетная запись заблокирована. Обратитесь к представителю службы поддержки, чтобы разблокировать ее, и повторите попытку. |
| **UserMessageIfUserAccountLocked** | Ваша учетная запись временно заблокирована, чтобы предотвратить ее несанкционированное использование. Повторите попытку позже. |
| **AADRequestsThrottled** | Сейчас слишком много запросов. Подождите и повторите попытку. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Элементы пользовательского интерфейса страниц для входа и самоподтверждения

Ниже приведены идентификаторы для определения содержимого с идентификатором `api.localaccountsignup` или любого определения содержимого, которое начинается с `api.selfasserted`, например `api.selfasserted.profileupdate` и `api.localaccountpasswordreset`.

| ID | Значение по умолчанию |
| -- | ------------- |
| **ver_sent** | Код проверки отправлен на следующий адрес: |
| **ver_but_default** | По умолчанию |
| **cancel_message** | Пользователь отменил ввод самостоятельно подтверждаемых данных |
| **preloader_alt** | Подождите |
| **ver_but_send** | Отправить код проверки |
| **alert_yes** | Да |
| **error_fieldIncorrect** | Некоторые поля заполнены неправильно. Проверьте введенные данные и повторите попытку. |
| **year** | Год |
| **verifying_blurb** | Подождите, пока мы обрабатываем ваши данные. |
| **button_cancel** | Отмена |
| **ver_fail_no_retry** | Вы ввели неправильный код слишком много раз. Повторите попытку позже. |
| **month** | Месяц |
| **ver_success_msg** | Адрес электронной почты проверен. Вы можете продолжать работу. |
| **months** | Январь, февраль, март, апрель, май, июнь, июль, август, сентябрь, октябрь, ноябрь, декабрь |
| **ver_fail_server** | Возникли проблемы с проверкой адреса электронной почты. Введите допустимый адрес электронной почты и повторите попытку. |
| **error_requiredFieldMissing** | Отсутствует обязательное поле. Заполните все обязательные поля и повторите попытку. |
| **initial_intro** | Укажите следующие сведения. |
| **ver_but_resend** | Отправить новый код |
| **button_continue** | Создание |
| **error_passwordEntryMismatch** | Пароли в полях не совпадают. Введите одинаковый пароль в оба поля и повторите попытку. |
| **ver_incorrect_format** | Неверный формат. |
| **ver_but_edit** | Изменить адрес электронной почты |
| **ver_but_verify** | Проверить код |
| **alert_no** | нет |
| **ver_info_msg** | Код проверки отправлен на ваш адрес. Скопируйте его в поле ввода ниже. |
| **day** | День |
| **ver_fail_throttled** | Отправлено слишком много запросов на проверку этого адреса электронной почты. Повторите попытку позже. |
| **helplink_text** | Что это такое? |
| **ver_fail_retry** | Этот код неправильный. Повторите попытку. |
| **alert_title** | Отмена ввода своих данных |
| **required_field** | Эта информация обязательна. |
| **alert_message** | Вы действительно хотите отменить ввод своих данных? |
| **ver_intro_msg** | Проверка не требуется. Нажмите кнопку "Отправить". |
| **ver_input** | Код проверки |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Сообщения об ошибках на страницах входа и самоподтверждения

| ID | Значение по умолчанию |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Пользователь с указанным идентификатором уже существует. Выберите другой идентификатор. |
| **UserMessageIfClaimNotVerified** | Утверждение не проверено: {0} |
| **UserMessageIfIncorrectPattern** | Недопустимый шаблон для: {0} |
| **UserMessageIfMissingRequiredElement** | Отсутствует обязательный элемент: {0} |
| **UserMessageIfValidationError** | Ошибка при проверке: {0} |
| **UserMessageIfInvalidInput** | Для {0} указаны недопустимые входные данные. |
| **ServiceThrottled** | Сейчас слишком много запросов. Подождите и повторите попытку. |

В следующем примере показано использование некоторых элементов пользовательского интерфейса на странице регистрации:

![Страница регистрации с именами элементов пользовательского интерфейса с меткой](./media/localization-string-ids/localization-sign-up.png)

В следующем примере показано использование некоторых элементов пользовательского интерфейса на странице регистрации, после того как пользователь щелкнул кнопку отправки кода проверки:

![Элементы пользовательского интерфейса на странице регистрации для проверки адреса электронной почты](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Элементы пользовательского интерфейса на странице аутентификации по номеру телефона

Ниже указаны идентификаторы, используемые для определения содержимого с идентификатором `api.phonefactor`.

| ID | Значение по умолчанию |
| -- | ------------- |
| **button_verify** | Позвонить мне |
| **country_code_label** | код страны; |
| **cancel_message** | Пользователь отменил многофакторную проверку подлинности |
| **text_button_send_second_code** | Отправить новый код |
| **code_pattern** | \\d{6} |
| **intro_mixed** | У нас записан ваш номер. Мы можем отправить код в SMS или позвонить по этому номеру для проверки подлинности. |
| **intro_mixed_p** | У нас записаны ваши номера. Выберите номер, по которому мы можем позвонить или отправить код в SMS для проверки подлинности. |
| **button_verify_code** | Проверить код |
| **requiredField_code** | Введите полученный код проверки |
| **invalid_code** | Введите полученный код из 6 цифр |
| **button_cancel** | Отмена |
| **local_number_input_placeholder_text** | номер телефона. |
| **button_retry** | Повторить попытку |
| **alternative_text** | У меня нет с собой телефона |
| **intro_phone_p** | У нас записаны ваши номера. Выберите номер, по которому мы можем позвонить для проверки подлинности. |
| **intro_phone** | У нас записан ваш номер. Мы позвоним по нему для проверки подлинности. |
| **enter_code_text_intro** | Введите ниже код проверки или  |
| **intro_entry_phone** | Введите ниже номер, по которому мы можем позвонить для проверки подлинности. |
| **intro_entry_sms** | Введите ниже номер, по которому мы можем отправить код в SMS для проверки подлинности. |
| **button_send_code** | Отправить код |
| **invalid_number** | Введите действительный номер телефона |
| **intro_sms** | У нас записан ваш номер. Мы отправим код в SMS для проверки подлинности. |
| **intro_entry_mixed** | Введите ниже номер, по которому мы можем отправить код в SMS или позвонить для проверки подлинности. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |У нас записаны ваши номера. Выберите номер, по которому мы можем отправить код в SMS для проверки подлинности. |
| **requiredField_countryCode** | Выберите код страны |
| **requiredField_number** | Введите номер телефона |
| **country_code_input_placeholder_text** |Страна или регион |
| **number_label** | номер телефона; |
| **error_tryagain** | Указанный номер телефона занят или недоступен. Проверьте номер и повторите попытку. |
| **error_incorrect_code** | Введенный код проверки не совпадает с нашим. Повторите попытку или запросите новый код. |
| **countryList** | {\"ПО УМОЛЧАНИЮ\":\"страна/регион\",\"AF\":\"Афганистан\",\"AX\":\"Аландские острова\",\"AL\":\"Албания\",\"DZ\":\"Алжир\",\"AS\":\"Американское Самоа\",\"AD\":\"Андорра\",\"AO\":\"Ангола\",\"AI\":\"Ангилья\",\"AQ\":\"Антарктида\",\"AG\":\"Антигуа и Барбуда\",\"AR\":\"Аргентина\",\"AM\":\"Армения\",\"AW\":\"Аруба\",\"AU\":\"Австралия\",\"AT\":\"Австрия\",\"AZ\":\"Азербайджан\",\"BS\":\"Багамы\",\"BH\":\"Бахрейн\",\"BD\":\"Бангладеш\",\"BB\":\"Барбадос\",\"BY\":\"Беларусь\",\"BE\":\"Бельгия\",\"BZ\":\"Белиз\",\"BJ\":\"Бенин\",\"BM\":\"Бермудские острова\",\"BT\":\"Бутан\",\"BO\":\"Боливия\",\"BQ\":\"Бонайре\",\"BA\":\"Босния и Герцеговина\",\"BW\":\"Ботсвана\",\"BV\":\"Остров Буве\",\"BR\":\"Бразилия\",\"IO\":\"Британская территория в Индийском океане\",\"VG\":\"Виргинские о-ва (Великобритания)\",\"BN\":\"Бруней\",\"BG\":\"Болгария\",\"BF\":\"Буркина-Фасо\",\"BI\":\"Бурунди\",\"CV\":\"Кабо-Верде\",\"KH\":\"Камбоджа\",\"CM\":\"Камерун\",\"CA\":\"Канада\",\"KY\":\"острова Кайман\",\"CF\":\"Центральная Африканская Республика\",\"TD\":\"Чад\",\"CL\":\"Чили\",\"CN\":\"Китай\",\"CX\":\"остров Рождества\",\"CC\":\"Кокосовые острова (Килинг)\",\"CO\":\"Колумбия\",\"KM\":\"Коморские Острова\",\"CG\":\"Конго\",\"CD\":\"Конго (ДРК)\",\"CK\":\"острова Кука\",\"CR\":\"Коста-Рика\",\"CI\":\"Кот-д’Ивуар\",\"HR\":\"Хорватия\",\"CU\":\"Куба\",\"CW\":\"Кюрасао\",\"CY\":\"Кипр\",\"CZ\":\"Чешская Республика\",\"DK\":\"Дания\",\"DJ\":\"Джибути\",\"DM\":\"Доминика\",\"DO\":\"Доминиканская Республика\",\"EC\":\"Эквадор\",\"EG\":\"Египет\",\"SV\":\"Сальвадор\",\"GQ\":\"Экваториальная Гвинея\",\"ER\":\"Эритрея\",\"EE\":\"Эстония\",\"ET\":\"Эфиопия\",\"FK\":\"Фолклендские острова\",\"FO\":\"Фарерские острова\",\"FJ\":\"Фиджи\",\"FI\":\"Финляндия\",\"FR\":\"Франция\",\"GF\":\"Французская Гвиана\",\"PF\":\"Французская Полинезия\",\"TF\":\"Французские Южные Территории\",\"GA\":\"Габон\",\"GM\":\"Гамбия\",\"GE\":\"Грузия\",\"DE\":\"Германия\",\"GH\":\"Гана\",\"GI\":\"Гибралтар\",\"GR\":\"Греция\",\"GL\":\"Гренландия\",\"GD\":\"Гренада\",\"GP\":\"Гваделупа\",\"GU\":\"Гуам\",\"GT\":\"Гватемала\",\"GG\":\"Гернси\",\"GN\":\"Гвинея\",\"GW\":\"Гвинея-Бисау\",\"GY\":\"Гайана\",\"HT\":\"Гаити\",\"HM\":\"остров Херд и острова МакДональд\",\"HN\":\"Гондурас\",\"HK\":\"Гонконг (САР)\",\"HU\":\"Венгрия\",\"IS\":\"Исландия\",\"IN\":\"Индия\",\"ID\":\"Индонезия\",\"IR\":\"Иран\",\"IQ\":\"Ирак\",\"IE\":\"Ирландия\",\"IM\":\"остров Мэн\",\"IL\":\"Израиль\",\"IT\":\"Италия\",\"JM\":\"Ямайка\",\"JP\":\"Япония\",\"JE\":\"Джерси\",\"JO\":\"Иордания\",\"KZ\":\"Казахстан\",\"KE\":\"Кения\",\"KI\":\"Кирибати\",\"KR\":\"Корея\",\"KW\":\"Кувейт\",\"KG\":\"Кыргызстан\",\"LA\":\"Лаос\",\"LV\":\"Латвия\",\"LB\":\"Ливан\",\"LS\":\"Лесото\",\"LR\":\"Либерия\",\"LY\":\"Ливия\",\"LI\":\"Лихтенштейн\",\"LT\":\"Литва\",\"LU\":\"Люксембург\",\"MO\":\"специальный административный район Макао\",\"MK\":\"Македония, БЮРМ\",\"MG\":\"Мадагаскар\",\"MW\":\"Малави\",\"MY\":\"Малайзия\",\"MV\":\"Мальдивы\",\"ML\":\"Мали\",\"MT\":\"Мальта\",\"MH\":\"Маршалловы острова\",\"MQ\":\"Мартиника\",\"MR\":\"Мавритания\",\"MU\":\"Маврикий\",\"YT\":\"Майотта\",\"MX\":\"Мексика\",\"FM\":\"Микронезия\",\"MD\":\"Молдова\",\"MC\":\"Монако\",\"MN\":\"Монголия\",\"ME\":\"Черногория\",\"MS\":\"Монтсеррат\",\"MA\":\"Марокко\",\"MZ\":\"Мозамбик\",\"MM\":\"Мьянма\",\"NA\":\"Намибия\",\"NR\":\"Науру\",\"NP\":\"Непал\",\"NL\":\"Нидерланды\",\"NC\":\"Новая Каледония\",\"NZ\":\"Новая Зеландия\",\"NI\":\"Никарагуа\",\"NE\":\"Нигер\",\"NG\":\"Нигерия\",\"NU\":\"Ниуэ\",\"NF\":\"остров Норфолк\",\"KP\":\"Северная Корея\",\"MP\":\"Северные Марианские острова\",\"NO\":\"Норвегия\",\"OM\":\"Оман\",\"PK\":\"Пакистан\",\"PW\":\"Палау\",\"PS\":\"Палестинская национальная администрация\",\"PA\":\"Панама\",\"PG\":\"Папуа-Новая Гвинея\",\"PY\":\"Парагвай\",\"PE\":\"Перу\",\"PH\":\"Филиппины\",\"PN\":\"о-ва Питкэрн\",\"PL\":\"Польша\",\"PT\":\"Португалия\",\"PR\":\"Пуэрто-Рико\",\"QA\":\"Катар\",\"RE\":\"Реюньон\",\"RO\":\"Румыния\",\"RU\":\"Россия\",\"RW\":\"Руанда\",\"BL\":\"Сен-Бартелеми\",\"KN\":\"Сент-Китс и Невис\",\"LC\":\"Сент-Люсия\",\"MF\":\"Сен-Мартен\",\"PM\":\"Сен-Пьер и Микелон\",\"VC\":\"Сент-Винсент и Гренадины\",\"WS\":\"Самоа\",\"SM\":\"Сан-Марино\",\"ST\":\"Сан-Томе и Принсипи\",\"SA\":\"Саудовская Аравия\",\"SN\":\"Сенегал\",\"RS\":\"Сербия\",\"SC\":\"Сейшельские Острова\",\"SL\":\"Сьерра-Леоне\",\"SG\":\"Сингапур\",\"SX\":\"Синт-Мартен\",\"SK\":\"Словакия\",\"SI\":\"Словения\",\"SB\":\"Соломоновы Острова\",\"SO\":\"Сомали\",\"ZA\":\"Южная Африка\",\"GS\":\"Южная Георгия и Южные Сандвичевы о-ва\",\"SS\":\"Южный Судан\",\"ES\":\"Испания\",\"LK\":\"Шри-Ланка\",\"SH\":\"Острова Святой Елены, Вознесения и Тристан-да-Кунья\",\"SD\":\"Судан\",\"SR\":\"Суринам\",\"SJ\":\"Шпицберген\",\"SZ\":\"Свазиленд\",\"SE\":\"Швеция\",\"CH\":\"Швейцария\",\"SY\":\"Сирия\",\"TW\":\"Тайвань\",\"TJ\":\"Таджикистан\",\"TZ\":\"Танзания\",\"TH\":\"Таиланд\",\"TL\":\"Тимор-Лешти\",\"TG\":\"Того\",\"TK\":\"Токелау\",\"TO\":\"Тонга\",\"TT\":\"Тринидад и Тобаго\",\"TN\":\"Тунис\",\"TR\":\"Турция\",\"TM\":\"Туркменистан\",\"TC\":\"Острова Теркс и Кайкос\",\"TV\":\"Тувалу\",\"UM\":\"Внешние малые острова США\",\"VI\":\"Виргинские острова США\",\"UG\":\"Уганда\",\"UA\":\"Украина\",\"AE\":\"ОАЭ\",\"GB\":\"Великобритания\",\"US\":\"США\",\"UY\":\"Уругвай\",\"UZ\":\"Узбекистан\",\"VU\":\"Вануату\",\"VA\":\"Ватикан\",\"VE\":\"Венесуэла\",\"VN\":\"Вьетнам\",\"WF\":\"Уоллис и Футуна\",\"YE\":\"Йемен\",\"ZM\":\"Замбия\",\"ZW\":\"Зимбабве\"} |
| **error_448** | Указанный номер телефона недоступен. |
| **error_449** | Пользователь превысил количество повторных попыток. |
| **verification_code_input_placeholder_text** | Код проверки |

В следующем примере показано использование некоторых элементов пользовательского интерфейса на странице регистрации многофакторной идентификации:

![Элементы пользовательского интерфейса на странице регистрации для проверки адреса электронной почты](./media/localization-string-ids/localization-mfa1.png)

В следующем примере показано использование некоторых элементов пользовательского интерфейса на странице проверки многофакторной идентификации:

![Элементы пользовательского интерфейса на странице регистрации для проверки адреса электронной почты](./media/localization-string-ids/localization-mfa2.png)

## <a name="verification-display-control-user-interface-elements"></a>Элементы пользовательского интерфейса элемента управления отображением проверки

Ниже приведены идентификаторы для [элемента управления отображением проверки](display-control-verification.md)

| ID | Значение по умолчанию |
| -- | ------------- |
|verification_control_but_change_claims |Change |
|verification_control_fail_send_code |Отправить код не удалось. Повторите попытку позже. |
|verification_control_fail_verify_code |Проверить код не удалось. Повторите попытку позже. |
|verification_control_but_send_code |Отправить код |
|verification_control_but_send_new_code |Отправить новый код |
|verification_control_but_verify_code |Проверить код |
|verification_control_code_sent| Код проверки отправлен. Скопируйте его в поле ввода ниже. |

### <a name="example"></a>Пример

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-mfa-error-messages"></a>Сообщения об ошибках Azure MFA

Ниже приведены идентификаторы для сообщения об ошибках [технического профиля Azure MFA](multi-factor-auth-technical-profile.md):

| ID | Значение по умолчанию |
| -- | ------------- |
|UserMessageIfCouldntSendSms | Не удается отправить SMS на телефон, попробуйте использовать другой номер телефона. |
|UserMessageIfInvalidFormat | Неправильный формат номера телефона, внесите исправления и повторите попытку.|
|UserMessageIfMaxAllowedCodeRetryReached | Неправильный код введен слишком много раз. Повторите попытку позже.|
|UserMessageIfServerError | Не удалось использовать службу MFA. Повторите попытку позже.|
|UserMessageIfThrottled | Ваш запрос отрегулирован, повторите попытку позже.|
|UserMessageIfWrongCodeEntered|Указан неправильный код, повторите попытку.|

### <a name="example"></a>Пример

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>Сообщения об ошибках одноразового пароля

Ниже приведены идентификаторы для сообщения об ошибках [технического профиля одноразового пароля](one-time-password-technical-profile.md):

| ID | Значение по умолчанию |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |При проверке подлинности превышено максимальное число попыток ввода одноразового пароля |
|UserMessageIfSessionDoesNotExist |Срок действия сеанса проверки пароля истек |
|UserMessageIfSessionConflict |Конфликт сеанса проверки одноразового пароля |
|UserMessageIfInvalidCode |Неправильный пароль, предоставленный для проверки |
|UserMessageIfVerificationFailedRetryAllowed |Этот код неправильный. Повторите попытку. | 

### <a name="example"></a>Пример

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```


## <a name="claims-transformations-error-messages"></a>Сообщения об ошибках преобразования утверждений

Ниже приведены идентификаторы сообщений об ошибках преобразования утверждений.

| ID | Преобразование утверждений | Значение по умолчанию |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | Сбой сравнения значений логического утверждения для типа утверждения "inputClaim".| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | Сбой сравнения значений утверждений: Указанный левый операнд больше правого операнда.|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | Сбой сравнения значений утверждений с использованием StringComparison "OrdinalIgnoreCase".|

### <a name="example"></a>Пример

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```










