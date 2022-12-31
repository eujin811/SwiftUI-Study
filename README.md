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
  - 값 자체 (데이터 값)이 아닌 **view에서 읽고 쓸 수 있는 값**
  - 데이터 컨트롤의 영역이 아니라 view에서 보여주거나 단순 표기하기 위한 값.
      - 뷰의 내부 혹은 뷰에서 호출된 메서드에서만 접근해야한다.
  - 단순히 view 내부에서 값을 읽고 변경하는 수단으로 외부에서 절대 사용하지 않을 간단한 property에 적합하다.
  - 뷰 내부에서만 사용할 수 있으므로 **private** 필수!

- 상태 값 변경 시 View body를 다시 계산해준다.
  - State 변경 시 뷰의 appearance 무효화하고 body를 다시 계산한다.
  - 상태 값에 접근해야하는 뷰들 중 가장 상단에 위치시켜야함 

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

State 사용예시
- 특정 행동에 맞춰 view의 상태 변경시 
- view enable
- toggle
- picker
- tabView

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
 
- toggle / 화면전환.

```swift
 struct ToggleView: View {
    @State private var isEnable = false
    @State private var showingSheet = false
    
    var body: some View {
        Form {
            Section {
                Toggle("버튼 활성화", isOn: $isEnable)
            }
            
            Section {
                Button("여기 여기!") {
                    print("click")
                    showingSheet.toggle()
                }
                .sheet(isPresented: $showingSheet) {
                    SegmentedControlView()
                }
                .disabled(isEnable == false)
            }
        }
    }
 }
```

- picker 
  - state로 값 변경 안하면 반응 x
  
```swift
 struct SegmentedControlView: View {
    @State private var selectedIndex = 0

    var body: some View {
        Picker("", selection: $selectedIndex) {
            Text("A").tag(0)
            Text("B").tag(1)
            Text("C").tag(2)
        }.pickerStyle(SegmentedPickerStyle())
    }
 }

```

- tabview 
  - tag 넣지 않아도 단순 선택으로 화면 이동은 되지만 특정 상황에서 화면 이동 하려면 state로 tag값 변경해야함.

```swift
 struct TestTabView: View {
    @State private var selectIndex = 0
    
    var body: some View {
        TabView(selection: $selectIndex) {
            FirstIndexView()
                .tabItem {
                    Label("1", systemImage: "house")
                }
                .tag(0)
            
            SecondIndexView()
                .tabItem {
                    Label("2", systemImage: "person")
                }
                .tag(1)
            
            ThirdIndexView()
                .tabItem {
                    Label("3", systemImage: "bag")
                }
                .tag(2)
        }
        
        Button(action : {
            selectIndex = (selectIndex + 1) % 3
        }) {
            Text("다음")
                .padding()
                .foregroundColor(.white)
                .background(Color.blue)
                .padding()
        }
    }
 }
```

## Observable
-

## Environmnet
- 
