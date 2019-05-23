---
title: Начало работы с Android и платформой удостоверений Майкрософт | Azure
description: Получение маркера доступа для приложения Android и вызов API Microsoft Graph или API, которые требуют маркеры доступа от платформы удостоверений Майкрософт.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6112facfc0c10d7a0a0495cd778fa6c3cb6130a7
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962154"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Вход пользователей и вызов Microsoft Graph из приложения Android

Из этого руководства вы узнаете, как интегрировать приложение Android с платформой удостоверений Майкрософт. В частности, приложение авторизует пользователя, получит маркер доступа для вызова API Microsoft Graph и выполнит запрос к API Microsoft Graph.  

Когда вы завершите работу с этим руководством, ваше приложение сможет принимать операции входа с помощью личных учетных записей Майкрософт (включая outlook.com, live.com и другие), а также рабочих или учебных учетных записей из любой компании или организации, в которых используется Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Как работает это руководство

![Схема работы примера приложения, создаваемого в этом кратком руководстве](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

В этом примере приложение авторизует пользователя и получает данные от его имени.  Доступ к этим данным будет осуществляться через защищенный API (в данном случае — API Microsoft Graph), требующий проверки подлинности.

В частности:

* Приложение авторизует пользователя через браузер или Microsoft Authenticator и Корпоративный портал Intune.
* Пользователь принимает разрешения, которые запрашивает приложение. 
* Приложение выдает маркер доступа для API Microsoft Graph.
* Этот маркер доступа будет включен в HTTP-запрос к веб-API.
* Затем обрабатывается ответ Microsoft Graph.

В этом примере используется библиотека проверки подлинности Майкрософт для Android (MSAL), которая помогает реализовать проверку подлинности. MSAL автоматически обновляет маркеры, обеспечивает единый вход для других приложений на устройстве, а также управляет учетными записями.

## <a name="prerequisites"></a>Предварительные требования

* Для этой пошаговой установки необходима среда Android Studio.
* Android 16 или более поздней версии (рекомендуется версия 19 и более поздняя).

## <a name="library"></a>Библиотека

В этом руководстве используется следующая библиотека аутентификации:

|Библиотека|ОПИСАНИЕ|
|---|---|
|[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)|Библиотека проверки подлинности Майкрософт (MSAL)|

## <a name="set-up-your-project"></a>Настройка проекта

В рамках этого руководства будет создан проект. Если вместо этого вы хотите скачать готовое руководство, [скачайте код](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>Создание нового проекта

1. Откройте Android Studio и выберите **Start a new Android Studio project** (Создать проект Android Studio).
    - Если Android Studio уже открыт, выберите **File** > **New** > **New Project** (Файл > Создать > Новый проект).
2. Оставьте значение **Empty Activity** (Пустое действие) без изменений и нажмите кнопку **Next** (Далее).
3. Присвойте приложению имя, задайте для параметра `Minimum API level` (Минимальный уровень API) значение **API 19 or newer** (API 19 или более поздней версии) и нажмите кнопку **Finish** (Готово).
5. В `app/build.gradle` установите для параметра `targetedSdkVersion` значение 27. 

## <a name="register-your-application"></a>Регистрация приложения

Приложение можно зарегистрировать одним из двух способов, которые описаны в следующих двух разделах.

### <a name="register-your-app"></a>Регистрация приложения

1. Перейдите на [портал Azure](https://aka.ms/MobileAppReg) и выберите `New registration` (Новая регистрация). 
2. Введите **имя** приложения и выберите `Register` (Зарегистрировать). **Не устанавливайте URI перенаправления на этом этапе**. 
3. В разделе `Manage` (Управление) перейдите к `Authentication` (Проверка подлинности) > `Add a platform` (Добавить платформу) > `Android`.
    - Введите имя пакета проекта. Если вы скачали код, это будет значение `com.azuresamples.msalandroidapp`. 
    - Введите хэш подписи для отладки и разработки. Создайте хэш подписи, используя команду KeyTool на портале. 
4. Щелкните `Configure` (Настроить) и сохраните ***конфигурацию MSAL*** для последующего использования. 

## <a name="build-your-app"></a>Создание приложения

### <a name="configure-your-android-app"></a>Настройка приложения Android

1. Щелкните правой кнопкой мыши **res** > **New** (Создать) > **Folder** (Папка) > **Raw Resources Folder** (Папка необработанных ресурсов).
2. В **app** > **res** > **raw** создайте JSON-файл с именем `auth_config.json` и вставьте свою ***конфигурацию MSAL***. Дополнительные сведения см. в статье о [настройке MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
   <!-- Workaround for Docs conversion bug -->
3. В **app** > **manifests** > **AndroidManifest.xml** добавьте действие `BrowserTabActivity`, как показано ниже. Эта запись позволяет корпорации Майкрософт выполнять обратный вызов приложения после завершения проверки подлинности:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Обратите внимание, что используемый хэш подписи не должен быть указан в формате URL-адреса в файле **AndroidManifest.xml**. 

4. В файле **AndroidManifest.xml** и непосредственно над тегом `<application>` добавьте следующие разрешения:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

5. В `BrowserTabActivity` замените ***имя пакета*** и ***хэш подписи*** значениями, зарегистрированными на портале Azure.

### <a name="create-the-apps-ui"></a>Создание пользовательского интерфейса приложения

1. Перейдите в раздел **res** > **layout**, а затем откройте файл **activity_main.xml**.
2. Замените макет действия `android.support.constraint.ConstraintLayout` или другой на `LinearLayout`.
3. Добавьте в узел `LinearLayout` свойство `android:orientation="vertical"`.
4. Вставьте следующий код в узел `LinearLayout`, заменив текущее содержимое:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>Добавление MSAL в проект

1. В Android Studio выберите **Gradle Scripts** (Скрипты Gradle) > **build.gradle (Module: app)**.
2. В разделе **Dependencies** (Зависимости) вставьте следующий код:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>Использование MSAL 

В следующих разделах будут вноситься изменения внутри `MainAcitivty.java`. Мы пошагово выполним все действия для добавления и использования MSAL в приложении.

#### <a name="required-imports"></a>Необходимые операторы import

Добавьте следующие операторы import в проект: 

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiating-msal"></a>Создание экземпляра MSAL 

Внутри класса `MainActivity` нужно создать экземпляр MSAL, а также несколько конфигураций, касающихся того, что будет выполнять приложение, включая области действия и веб-API, к которым необходимо получить доступ. 

Скопируйте в `MainActivity` приведенные ниже переменные:

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

Теперь для создания экземпляра MSAL скопируйте следующий код внутри метода `onCreate(...)`:

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

Приведенный выше блок кода пытается автоматически выполнить вход пользователей при открытии приложения с помощью `getAccounts(...)` и, в случае успешного выполнения, `acquireTokenSilentAsync(...)`.  В следующих разделах мы реализуем обработчик обратных вызовов на случай, если в системе нет учетных записей, с которых выполнен вход. 

#### <a name="use-msal-to-get-tokens"></a>Использование MSAL для получения маркеров

Теперь мы можем реализовать логику обработки пользовательского интерфейса приложения и интерактивно получать маркеры через MSAL. 

MSAL предоставляет два основных метода получения маркеров: `acquireTokenSilentAsync` и `acquireToken`.  

`acquireTokenSilentAsync` выполняет вход и получает маркеры без какого-либо взаимодействия с пользователем при условии наличия учетной записи. В случае успешного входа MSAL передаст маркеры в приложение, а случае сбоя создаст исключение `MsalUiRequiredException`.  Если это исключение создано или пользователю требуется интерактивный вход (могут потребоваться учетные данные, многофакторная проверка подлинности или другие политики условного доступа), тогда можно использовать `acquireToken`.  

`acquireToken` всегда будет отображать пользовательский интерфейс при попытке входа и получения маркеров. Однако для обеспечения интерактивного единого входа могут использоваться файлы cookie сеанса в браузере или учетная запись в Microsoft Authenticator. 

Чтобы начать, необходимо создать следующие три метода пользовательского интерфейса внутри класса `MainActivity`:

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

Добавьте метод для получения текущего действия и обработки автоматических и интерактивных обратных вызовов:

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>Использование MSAL для выхода

Теперь мы добавим поддержку для выхода в приложении. 

Следует отметить, что в случае выхода с помощью MSAL из этого приложения удаляется вся известная информация о пользователе, но у пользователя все еще будет активный сеанс на его устройстве. Если пользователь пытается войти снова, он может увидеть взаимодействие, но повторный ввод учетных данных может не потребоваться из-за активного сеанса устройства. 

Чтобы добавить выход, скопируйте следующий метод в приложение, которое выполняет циклический переход по всем учетным записям и удаляет их:

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>Вызов API Microsoft Graph

Как только мы успешно получили маркер, мы можем выполнить запрос к API Microsoft Graph. Маркер доступа будет содержаться в `AuthenticationResult` внутри метода обратного вызова проверки подлинности `onSuccess(...)`. Чтобы создать авторизованный запрос, вашему приложению потребуется добавить маркер доступа в заголовок HTTP:

| ключ заголовка    | value                 |
| ------------- | --------------------- |
| Авторизация | Bearer <маркер доступа> |

Чтобы сделать это в коде, добавьте следующие два метода в приложение для вызова графа и обновления пользовательского интерфейса: 

```java
    /* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

Дополнительные сведения об [API Microsoft Graph](https://graph.microsoft.com).

#### <a name="multi-account-applications"></a>Приложения для нескольких учетных записей

Это приложение создано для сценария с одной учетной записью. MSAL также поддерживает сценарии с несколькими учетными записями, но это требует дополнительной работы с приложениями. Вам нужно будет создать пользовательский интерфейс, чтобы помочь пользователю выбрать нужную учетную запись для каждого действия, для которого требуются маркеры. Кроме того, приложение может реализовать эвристику для выбора нужной учетной записи с помощью метода `getAccounts(...)`. 

## <a name="test-your-app"></a>Тестирование приложения

### <a name="run-locally"></a>Локальный запуск

Если вы следовали приведенному выше коду, попробуйте создать и развернуть приложение на устройстве для тестирования или эмуляторе. У вас должна быть возможность входить и получать маркеры для учетных записей Azure AD или личных учетных записей Майкрософт. После входа пользователя это приложение будет отображать данные, возвращенные из конечной точки `/me` Microsoft Graph. 

Если у вас возникли какие-либо проблемы, вы можете сообщить о проблеме в этом документе или в библиотеке MSAL и связаться с нами. 

### <a name="consent-to-your-app"></a>Предоставление согласия для приложения

При первом входе любого пользователя в приложение от платформы удостоверений Майкрософт появится запрос на предоставление согласия на запрашиваемые разрешения.  Хотя большинство пользователей могут дать согласие, некоторые арендаторы Azure AD отключили согласие пользователей, требуя, чтобы администраторы давали согласие от имени всех пользователей.  Для поддержки этого сценария обязательно зарегистрируйте области своего приложения на портале Azure.

## <a name="help-and-support"></a>Справка и поддержка

Возникли какие-либо проблемы с этим руководством или с платформой удостоверений Майкрософт? Ознакомьтесь со статьей [Возможности получения поддержки и справки для разработчиков](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options).
