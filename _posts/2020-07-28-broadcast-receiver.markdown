---
layout: post
title:  "[Android] 브로드캐스트 리시버(BroadCastReceiver)"
subtitle:   "Android"
categories: devlog
tags: android
comments: true
---

## BroadCast Receiver 이해

다양한 시스템 이벤트가 발생 하였을때 시스템은 자동으로 이벤트를 수신하도록 신청한 모든 앱에 브로드캐스트를 전송합니다. 브로드캐스트 메시지는 Intent 객체로 래핑되어 보내지고 앱에서는 인텐트를 읽고 처리합니다. 코드에서 인텐트를 준비하고 시스템에 의뢰하면 시스템에서 인텐트 정보에 맞는 컴포넌트를 실행하는 구조 입니다. 하지만 액티비티 인텐트와는 약간의 차이가 있습니다.

#### 액티비티 인텐트와 차이

**액티비티 인텐트**

1. 인텐트 정보와 맞는 액티비티가 없을 경우 인텐트가 발생한 곳에서 **에러**
2. 인텐트가 발생하였는데 실행할 액티비티가 두개 이상인 경우 **사용자 선택으로 하나만 실행**

**브로드캐스트 리시버 인텐트**

1. 인텐트 정보로 실행할 브로드캐스트 리시버가 없으면 **아무 일도 발생하지 않음**
2. 인텐트 발생으로 실행할 브로드캐스트 리시버가 여러개라면 **모두 실행**


## 브로드캐스트 리시버 작성방법

<span style="color:red">
Android 8.0(API 레벨 26) 백그라운드 실행 제한의 일환으로 API 레벨 26 이상을 타겟팅하는 앱은 암시적 브로드캐스트의 Broadcast Receiver를 manifest에 더 이상 등록할 수 없습니다.</span> 그래서 앞으로는 브로드캐스트 리시버를 등록할 때 액티비티 내에서 동적으로 등록해 주어야 합니다.


**AndroidManifest.xml**

```kotlin
        <receiver
            android:name=".MyBroadcastReceiver"
            android:enabled="true"
            android:exported="true">
        </receiver>
```

**MyBroadcastReceiver.kt**

```kotlin
private const val TAG = "MyBroadcastReceiver"

class MyBroadcastReceiver : BroadcastReceiver() {

    override fun onReceive(context: Context?, intent: Intent?) {
        when (intent?.action) {
            Intent.ACTION_POWER_CONNECTED -> Toast.makeText(
                context,
                "Power Connected",
                Toast.LENGTH_SHORT
            ).show()

            Intent.ACTION_POWER_DISCONNECTED -> Toast.makeText(
                context,
                "Power Disconnected",
                Toast.LENGTH_SHORT
            ).show()
        }
    }


}
```


**MainActivity.kt**

```kotlin
class MainActivity : AppCompatActivity() {

    private val myBroadcastReceiver = MyBroadcastReceiver()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }

    override fun onResume() {
        super.onResume()
        val filter = IntentFilter()
        filter.addAction(Intent.ACTION_POWER_CONNECTED)
        filter.addAction(Intent.ACTION_POWER_DISCONNECTED)

        //수신자 등록
        registerReceiver(myBroadcastReceiver, filter)
    }

    override fun onPause() {
        //수신 중지
        unregisterReceiver(myBroadcastReceiver)
        super.onPause()
    }
}

