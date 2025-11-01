```markdown
---
title: "Flutter AppAuth OIDC Redirection 문제 해결"
date: 2025-11-1
draft: false
tags: ["flutter", "oidc", "appauth", "authentication"]
---

## 문제 상황

Flutter AppAuth 라이브러리를 사용하여 OIDC(OpenID Connect) 인증을 구현할 때 리다이렉션 관련 문제가 발생했습니다.

GitHub Issue: [flutter_appauth#503](https://github.com/MaikuB/flutter_appauth/issues/503)

## 해결 방법

해당 이슈의 [댓글](https://github.com/MaikuB/flutter_appauth/issues/503#issuecomment-2165906205)에서 제시된 해결 방법을 적용했습니다.

### Android 설정

`android/app/src/main/AndroidManifest.xml` 파일에 다음 설정을 추가:

```xml
<activity
    android:name="net.openid.appauth.RedirectUriReceiverActivity"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.VIEW"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <category android:name="android.intent.category.BROWSABLE"/>
        <data android:scheme="your-custom-scheme"/>
    </intent-filter>
</activity>
```

### iOS 설정

`ios/Runner/Info.plist` 파일에 URL scheme 추가:

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>your-custom-scheme</string>
        </array>
    </dict>
</array>
```

## 결과

위 설정을 적용한 후 OIDC 인증 후 리다이렉션이 정상적으로 작동하게 되었습니다.
```