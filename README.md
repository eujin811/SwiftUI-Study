# SwiftUI-Study

## Contents
- [State](https://github.com/eujin811/SwiftUI-Study/blob/main/README.md#state)
- [Observable](https://github.com/eujin811/SwiftUI-Study/blob/main/README.md#observable)
- [Environment](https://github.com/eujin811/SwiftUI-Study/blob/main/README.md#environmnet)



## State
- **State property** (@State)
- **Binding** (@Binding)

**State property**
- 뷰에 속한 값, 뷰에 바인딩할 값.
- view layout에 표현할 상태 값.
- RxSwift의 Relay와 비슷한 역할.
- 뷰 내부에서만 사용할 수 있음 (접근제한자 private 필수.)

- 사용법
  - @State private var ~ : state 담을 변수 
  - ${변수명}: 데이터 바인딩, 데이터 저장
  - {변수명}: 데이터 사용, 데이터 참조 
```swift
 struct ContentView: View {
    @State private var wifiEnbled = true
    @State private var userName = String()
    
    
    var body: some View {
        VStack {
            TextField("Enter user name", text: $userName)
            Text(userName)
        }
        .padding()
    }
 }

```

**Binding**
- 하위 뷰에 State값 전달 시 사용.
- @Binding var ~: 상위뷰에서 전달받아 사용할 변수

```swift
 struct ContentView: View {
    @State private var wifiEnbled = true
    
    var body: some View {
        VStack {
            Toggle(isOn: $wifiEnbled) {
                Text("enable Wi-Fi")
            }
            
            Image(systemName: wifiEnbled ? "wifi" : "wifi.slash")
            WifiImageView(wifiEnabled: $wifiEnbled)
        }
        .padding()
    }
 }

 struct WifiImageView: View {
    @Binding var wifiEnabled: Bool
    
    var body: some View {
        Image(systemName: wifiEnabled ? "wifi" : "wifi.slash")
    }
 }

```


## Observable
-

## Environmnet
- 
