---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/14/2020
ms.author: nikuklic
ms.openlocfilehash: 5c20543caf5bf623738996ed01064d71a0745c04
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779827"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно. 
- Развернутый ресурс Служб коммуникации. [Создайте ресурс Служб коммуникации.](../../create-communication-resource.md)
- Номер телефона, полученный из ресурса Служб коммуникации. [Как получить номер телефона.](../../telephony-sms/get-phone-number.md)
- `User Access Token` для включения клиента вызова. Дополнительные сведения о том, [как получить `User Access Token`](../../access-tokens.md)
- Выполните инструкции из краткого руководства [Начало работы путем добавления вызова в приложение](../getting-started-with-calling.md).

### <a name="prerequisite-check"></a>Проверка предварительных условий

- Чтобы просмотреть номера телефонов, связанные с ресурсом Служб коммуникации, войдите на [портал Azure](https://portal.azure.com/), перейдите к ресурсу Служб коммуникации и откройте вкладку с **номерами телефонов** в области навигации слева.

## <a name="setting-up"></a>Настройка

### <a name="add-pstn-functionality-to-your-app"></a>Добавление возможностей PSTN в приложение

Добавьте тип `PhoneNumber` в приложение, изменив файл **MainActivity.java**:


```java
import com.azure.android.communication.common.PhoneNumber;
```

<!--
> [!TBD]
> Namespace based on input from Komivi Agbakpem. But it does not correlates with other use namespaces in Calling Quickstart. E.g: "com.azure.communication.calling.CommunicationUser" or "com.azure.communication.common.client.CommunicationUserCredential". Double-chek this.
-->

## <a name="start-a-call-to-phone"></a>Вызов телефонного номера

Укажите номер телефона, полученный из ресурса Служб коммуникации. Он будет использоваться для начала вызова:

> [!WARNING]
> Обратите внимание, что номера телефонов должны быть указаны в формате международного стандарта E.164, например, +12223334444.

Измените обработчик событий `startCall()` в файле **MainActivity.java**, чтобы он обрабатывал телефонные вызовы:

```java
    private void startCall() {
        EditText calleePhoneView = findViewById(R.id.callee_id);
        String calleePhone = calleePhoneView.getText().toString();
        PhoneNumber callerPhone = new PhoneNumber("+12223334444");
        StartCallOptions options = new StartCallOptions();
        options.setAlternateCallerId(callerPhone);
        options.setVideoOptions(new VideoOptions(null));
        call = agent.call(
                getApplicationContext(),
                new PhoneNumber[] {new PhoneNumber(calleePhone)},
                options);
    }
```

## <a name="launch-the-app-and-call-the-echo-bot"></a>Запуск приложения и вызов эхо-бота

Теперь вы можете запустить приложение с помощью кнопки Run App (Запустить приложение) на панели инструментов или нажав клавиши SHIFT+F10. Вы можете позвонить на телефон, указав номер телефона в добавленном текстовом поле и нажав кнопку **Вызов**.
> [!WARNING]
> Обратите внимание, что номера телефонов должны быть указаны в формате международного стандарта E.164, например, +12223334444.

![Снимок экрана с готовым приложением.](../media/android/quickstart-android-call-pstn.png)
