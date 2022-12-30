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
- RxSwift의 Relay와 비슷한 역할.
- view layout에 표현할 상태 값으로 값이 변할 때 body를 다시 계산해준다.
- 단순히 view 내부에서 값을 읽고 변경하는 수단으로 외부에서 절대 사용하지 않을 간단한 propery에 적합하다.
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

사용예시
- 특정 행동에 맞춰 view의 상태 변경시 

- view enable

```swift
  struct ContentView: View {
     @State private var userName = String()
    @State private var isEnable: Bool = false

    var body: some View {
        VStack {
            TextField("Enter user name", text: $userName)
                .disabled(!isEnable)
                .foregroundStyle(isEnable ? .primary : .secondary)
            
            Text(userName)

            Button(isEnable ? "enable" : "disable") {
                isEnable.toggle()
            }
            
            EnableButton(isEnable: $isEnable) // Pass a binding.
        }
    }
 
  }
  
  struct EnableButton: View {
    @Binding var isEnable: Bool

    var body: some View {
        Button(isEnable ? "enable" : "disable") {
            isEnable.toggle()
        }
    }
 }
 ```

- segment 


## Observable
-

## Environmnet
- 
