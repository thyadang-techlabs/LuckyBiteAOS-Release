# 럭키버스 안드로이드 연동 가이드

## 설치

1. 프로젝트의 app 디렉토리 안에 libs 디렉토리를 만들고, 릴리즈 페이지에서 다운 받은 .aar 파일을 옮기세요.
2. settings.gradle 파일에 아래 코드를 추가하세요.

```groovy
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.PREFER_SETTINGS)
    repositories {
        google()
        mavenCentral()
        maven { url = uri("https://bidmad-sdk.s3.amazonaws.com/") } //Bidmad
        flatDir {
            dirs 'app/libs'
        }

    }
}
```

3. 앱 레벨 build.gradle에 아래와 같은 코드를 추가하세요.

```groovy
dependencies {
	...
    	implementation(name: 'LuckyVerse', ext: 'aar')
	implementation("ad.helper.openbidding:admob-obh:3.20.0")
	implementation("com.adop.sdk:bidmad-androidx:3.20.0")
	implementation("com.adop.sdk.adapter:admob:22.0.0.6")
	implementation("com.google.android.gms:play-services-ads:22.6.0")
	...
}
```

4. AndroidManifest.xml 파일에 아래 코드들을 추가하세요.

```xml
...
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
<uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES" />
...

...
<meta-data android:name="com.google.android.gms.ads.APPLICATION_ID" android:value="테크랩스로부터 전달받은 애드몹 광고 키"/>
<meta-data android:name="com.adop.sdk.APP_DOMAIN" android:value="테크랩스로부터 발급받은 앱 도메인"/>
...
```

## 초기화

```kotlin
import com.techlabs.luckyverse.core.LuckyVerse

...
///럭키버스 SDK를 초기화합니다.
LuckyVerse.instance().initialize(activity: this)

///광고 식별자를 세팅합니다. 안드로이드의 adid 값을 넣어주시면 됩니다.
LuckyVerse.instance().updateADID("SAMPLE_ADID")

///사용자 식별자를 세팅합니다. 럭키버스 SDK를 사용하는 앱에서, 사용자를 식별할 때 사용하는 키를 넣어주시면 됩니다.
LuckyVerse.instance().updateUserId("사용자_식별자")

///앱 식별자를 세팅합니다. 테크랩스에서 발급한 AppKey를 넣어주시면 됩니다.
LuckyVerse.instance().updateAppKey("발급_받은_앱_키")

...
```

LuckyVerse 싱글톤 클래스의 `initialize(activity:Activity)` 함수를 실행하세요. 럭키버스를 초기화합니다.

LuckyVerse 싱글톤 클래스의 `updateUserId` 함수를 사용해, 사용자 식별자를 입력하세요. `updateUserId` 는 String 타입 변수를 인자로 받습니다. 매체사 앱에서 사용자를 식별할 때 사용하는 아이디를 입력하면 됩니다. (ex. 하나머니 앱 내 고객번호를 암호화 한 값)

LuckyVerse 싱글톤 클래스의 `updateAppKey` 함수를 이용해, 태크랩스로부터 발급받은 앱키를 입력하세요. `updateAppKey` 함수는 String 타입 변수를 인자로 받습니다. 앱 키는 보안과 매체사 식별을 위해 테크랩스에서 발급한 값입니다.

LuckyVerse 싱글톤 클래스의 `updateADID`  함수를 이용해, 사용자 디바이스로부터 전달받은 adid를 입력하세요. 광고 추적을 위해 사용됩니다.

## 광고 준비

```kotlin
import com.techlabs.luckyverse.core.LuckyVerse
...
LuckyVerse.instance().setInterstitialAdZoneId("전면_광고_존_아이디")
LuckyVerse.instance().setRewardAdZoneId("보상형_광고_존_아이디")
...

```

LuckyVerse를 import하세요. 그후 LuckyVerseSDK 싱글톤 클래스의 `setRewardAdZoneId` 함수와 `setInterstitialAdZoneId` 함수를 실행하세요. 

`setRewardAdZoneId` 함수는 String 타입 변수를 인자로 받습니다. 인자는 보상형 광고 송출을 위한 존 아이디입니다. 테크랩스에 문의해 발급 받으세요. (* 존 아이디를 입력하지 않을 경우, 테스트 광고가 송출됩니다.)

`setInterstitialAdZoneId` 함수는 String 타입 변수를 인자로 받습니다. 인자는 전면 광고 송출을 위한 존 아이디입니다. 테크랩스에 문의해 발급 받으세요. (*존 아이디를 입력하지 않을 경우, 테스트 광고가 송출됩니다.)

## 네이티브 UI 요소를 통한 럭키버스 진입

```kotlin
import com.techlabs.luckyverse.core.LuckyVerse
...
//럭키버스 메인 화면으로 진입
LuckyVerse.instance().openLuckyVerseMain(activity)

//럭키버스 "내 정보" 화면으로 진입
LuckyVerse.instance().openLuckyVerseMyPage(activity)

//럭키버스 "사주정보 입력" 화면으로 진입
LuckyVerse.instance().openLuckyVerseSajuInfo(activity)
...

```

클릭/터치 이벤트에 LuckyVerse 싱글톤 클래스의 “`open`”으로 시작하는 함수를 연결하세요. 럭키버스의 원하는 페이지로 이동하게 됩니다.

## 럭키버스에서 매체사 앱의 설정 화면으로 이동하기

```kotlin
//Kotlin langaue

import com.techlabs.luckyverse.core.LuckyVerse

LuckyVerse.instance().setGoToSettingCallback {
            // 설정 화면으로 이동하는 로직을 이곳에 작성해주세요.
            // 럭키버스 내에서 앱의 설정화면으로 이동해야 할 경우, 이곳에 작성된 로직이 호출됩니다.
}
```

```kotlin
//Java Language
LuckyVerse.instance().setGoToSettingCallback(()-> {
	/// 설정 화면으로 이동하는 로직을 이곳에 작성해주세요.
	/// 럭키버스 내에서 앱의 설정화면으로 이동해야 할 경우, 이곳에 작성된 로직이 호출됩니다.
	return null;
});
```

럭키버스 안에서 특정 요소를 터치했을 때, 앱의 설정화면으로 이동해야 할 경우가 있습니다. `setGoToSettingCallback` 함수에 콜백함수를 전달해주세요.
