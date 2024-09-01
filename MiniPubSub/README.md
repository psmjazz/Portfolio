# MiniPubSub

## 개요
유니티 혹은 언리얼 엔진이 안드로이드/iOS 의 특정 기능에 대한 인터페이스가 없거나, 안드로이드/iOS 용 SDK 를 사용하려면  
jni 나 c 라이브러리를 직접 연동해야 합니다.  
연동을 위해서는 java와 objective-c 로 추가적인 브릿지 인터페이스를 만들어야 합니다.  
MiniPubSub은 유니티/언리얼 엔진과 네이티브 sdk 사이에 통일된 인터페이스를 가진 통신 모듈로, 더 쉽고 빠르게 연동을 할 목적으로 만들어 졌습니다. 

## 구조
![MiniPubSub_Sctucture](./Images/MiniPubSub_Structure_flow.drawio.png)

### 흐름
1. Messenger 객체에 특정 키에 대해 Subscribe 를 하여 Message를 수신하도록 설정합니다.
2. MiniPubSub 바깥 게임 혹은 네이티브에서는 Message 객체를 사용해 데이터를 주고 받습니다.
3. MiniPubSub 내부에 도착한 메세지는 MessageMediator 가 적절한 Messener 혹은 Watcher 에게 분배합니다.
4. Watcher 는 발생한 모든 메세지를 열람할 수 있는 특수한 개체입니다. 모든 메세지를 받아 네이티브로 보낼 준비를 합니다.
5. Bridge는 메세지를 json 형태로 가공해 네이티브의 Bridge 에 전달합니다.
6. 네이티브 Bridge 는 다시 메세지 객체로 변환해 MessageMediator 에게 전달합니다.
7. MessageMediator 는 적절한(즉 해당 메세지의 키를 구독한) Messenger들에게 분배합니다.
8. Subscribe를 수행한 Messenger 객체로부터 메세지를 전달받습니다.
9. Publisher(수신 불가 객체) 를 통해 메세지 전달만을 할 수도 있고
10. 다른 Messenger 객체를 통해서 메세지를 전달 받을 수도 있습니다.

## 주요 클래스

### Message
데이터를 주고받기 위해 사용하는 객체.
- 키 : 해당 메세지의 키. 사전에 해당 키에 Subscribe 한 Messenger 객체는 해당 메세지를 수신할 수 있습니다.
- 데이터 : json 으로 구조화 할 수 있는 형식의 데이터 객체. 다른 수신자에게 전달하고자 하는 내용입니다.  
- 메세지는 다음과 같은 json으로 변경되어 전달됩니다.
```json
{
    "key": "Initialize",
    "Data": {
        "host": "example.com",
        "id": "adcd",
        "whitelist": [
            "aaaa",
            "bbbb",
            "cccc"
        ]
    }
}
```
### Publisher
메세지를 송신 할 수 있지만 수신은 불가능한 객체입니다.
- Publish : 메세지를 전송합니다.

### Messenger
메세지를 송수신할 수 있는 객체입니다.
- Subscribe : 키를 등록하여 해당 키와 같은 메세지를 받습니다.
- Publish : 메세지를 전송합니다.

### Watcher
발생하는 모든 메세지를 수신하는 객체.  
Watcher를 통해 발생한 모든 메세지를 엔진 
- 네이티브 사이에 전달하게 됩니다.
- Watch : 발생한 모든 메세지를 수신합니다.

### MessageMediator
전송한 메세지를 받아 Subscribe 한 Messenger 들에게 메세지를 전달합니다.

## 프로젝트 링크
- Unity : https://github.com/psmjazz/MiniPubSub-Unity/tree/dev/0.1.1/Packages/MiniPubSub
- Unreal : https://github.com/psmjazz/MiniPubSub-Unreal/tree/dev/0.1.0/Plugins/MiniPubSub
- Android : https://github.com/psmjazz/MiniPubSub-Android/tree/dev/0.1.1
- iOS : https://github.com/psmjazz/MiniPubSub-iOS/tree/dev/0.1.1