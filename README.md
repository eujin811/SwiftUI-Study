# SwiftUI-Study

## Contents
- [State](https://github.com/eujin811/SwiftUI-Study/blob/main/README.md#state)
  - 사용법
  - 다시 그려지는 시점. 
  - 예시
- [Observable](https://github.com/eujin811/SwiftUI-Study/blob/main/README.md#observable)
- [Environment](https://github.com/eujin811/SwiftUI-Study/blob/main/README.md#environmnet)



## State
- **State property** (@State)
- **Binding** (@Binding)

**State property**
- 뷰에 속한 값, 뷰에 바인딩할 값.
  - 값 자체 (데이터 값)이 아닌 **view에서 읽고 쓸 수 있는 값**
  - 선언된 뷰에 속한 뷰에 종속된 값으로 뷰가 사라지면 뷰의 state도 사라진다.
    - 데이터 컨트롤의 영역이 아니라 view에서 보여주거나 단순 표기하기 위한 값.
    - 뷰의 내부 혹은 뷰에서 호출된 메서드에서만 접근해야한다.
  - 단순히 view 내부에서 값을 읽고 변경하는 수단으로 외부에서 절대 사용하지 않을 간단한 property에 적합하다.
  - 뷰 내부에서만 사용할 수 있으므로 **private** 필수!

- 상태 값 변경 시 View body를 다시 계산해준다.
  - State 변경 시 뷰의 appearance 무효화하고 body를 다시 계산한다.
  - 상태 값에 접근해야하는 뷰들 중 가장 상단에 위치시켜야함 

- 사용법
  - @State private var ~ : state 담을 변수 
  - ${변수명}: 값 바인딩, 값 저장
  - {변수명}: 값 사용, 값 참조 


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


**State는 값이 변경될 경우 body를 다시 계산해준다**
- State의 값이 바뀔때만. (같은 값이 여러번 들어올 경우 x)
- body안의 모든 view들이 다시 그려짐

```swift
 struct StateRedrawView: View {
    @State var firstTitle = "안녕하세요!"
    @State var secondTitle = "좋은하루 보내셨나용?"
    @State var thirdTitle = "아니라구요? "
    
    var body: some View {
        VStack {
            Text(firstTitle)
                .background(.random)
                
            Text(secondTitle)
                .background(.random)
            
            Text(thirdTitle)
                .background(.random)
            
            Button("Change first title") {
                firstTitle = "오늘 퇴근 몇시에 하세요?"
            }
        }
    }
 }

 fileprivate extension ShapeStyle where Self == Color {
    static var random: Color {
        Color(
            red: .random(in: 0...1),
            green: .random(in: 0...1),
            blue: .random(in: 0...1))
    }
 }
 
```

- 새로운 값으로 변경된 상황(firstTitle 변경 시)에만 view들이 다시 그려진다.
  - body 내부의 모든 뷰들이 다시 그려짐 (background color 모두 변경) + 값 변경
  - button을 여러번 눌러도 최고 변경이외에 반응 없음 

- 값의 변화가 있어야 다시 그려진다.
  - 하단 예시의 경우 thirdTitle의 background는 변경되지 않음. 
  - ThirdView 내부의 값이 변화가 없기 때문.
  - 단, StateRedrawView의 thirdView그리는 부분에서 실행시 변화.
  
``` swift
 struct StateRedrawView: View {
    @State private var firstTitle = "안녕하세요!"
    @State private var secondTitle = "좋은하루 보내셨나용?"
    @State private var thirdTitle = "아니라구요? "
    
    var body: some View {
        VStack {
            Text(firstTitle)
                .background(.random)
                
            Text(secondTitle)
                .background(.random)
            
            ThirdView(text: thirdTitle)
            
            Button("Change first title") {
                firstTitle = "오늘 퇴근 몇시에 하세요?"
            }
        }
    }
 }

 fileprivate struct ThirdView: View {
    let text: String    
    
    var body: some View {
        Text(text)
            .background(.random)  
    }
 }

```

  - 하단의 경우 firstTitle의 값이 바뀌는 view 내부에서 ThirdView가 있어서 body가 다시 그려져 background 변경됨.
    ```swift
       struct StateRedrawView: View {
          @State private var firstTitle = "안녕하세요!"
          @State private var secondTitle = "좋은하루 보내셨나용?"
          @State private var thirdTitle = "아니라구요? "
     
          var body: some View {
              VStack {
                  Text(firstTitle)
                      .background(.random)
                
                 Text(secondTitle)
                     .background(.random)
            
                  ThirdView(text: thirdTitle)
                      .background(.random)    // 다시 그려짐
            
                  Button("Change first title") {
                      firstTitle = "오늘 퇴근 몇시에 하세요?"
                  }
              }
           }
        }

       fileprivate struct ThirdView: View {
         let text: String    
    
          var body: some View {
              Text(text)
      //            .background(.random)  // 다시 x
         }
       }
      ```

- State를 변경했을 때 하위 뷰까지 다시 그려주고 싶을 경우 
  - 하위뷰에 변경될 state를 넘겨준다. (Binding해준당!)

```swift

struct StateRedrawView: View {
    @State private var firstTitle = "안녕하세요!"
    @State private var secondTitle = "좋은하루 보내셨나용?"
    @State private var thirdTitle = "아니라구요? "
    
    var body: some View {
        VStack {
            Text(firstTitle)
                .background(.random)
            
            SecondView(text: $secondTitle, firstTitle: $firstTitle)
            
            ThirdView(text: thirdTitle)
            
            Button("Change first title") {
                firstTitle = "오늘 퇴근 몇시에 하세요?"
            }
        }
    }
}

fileprivate struct SecondView: View {
    @Binding var text: String
    @Binding var firstTitle: String
    
    var body: some View {
        HStack {
            Text(text)
                .background(.random)
            
            // 직접적인 값 변경을 하위 뷰에서 하지 않아도 변경됨.
            // 해당 button 주석처리해도 변경! 
//            Button("Change first title") {
//                firstTitle = "지금!"
//            }
        }
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
- ObservableObject 프로토콜을 따르는 형태
- 각각의 뷰들이 외부에서 데이터를 전달받아야 할 때 표현
- 변경되야 할 데이터들의 값을 모아 관리하는 역할
- 뷰에서는 초기화 상태에서 주입되어야 한다.
- Observable 객체는 Published property로서 데이터 값을 게시(publish)한다. / 데이터 선언부
  - @Published var ~
- Observer 객체는 게시자를 구독(Subscribe)하여 published property가 변경될 때마다 업데이트 받는다. / view binding
  - @ObservedObject var ~

**Observable 사용법**
```swift
  DemoView(demoData: DemoData())
```

```swift
 class DemoData: ObservableObject {
    @Published var userCount = 0
    @Published var currentUser = String()
    
    init() {}
    
    func updateData() {}
 }

 struct DemoView: View {
    @ObservedObject var demoData: DemoData
    
    var body: some View {
        VStack {
            Text("\(demoData.currentUser), number \(demoData.userCount)")
            
            Button("눌러봐!") {
                demoData.currentUser = "나!"
                demoData.userCount += 1
            }
        }
        .padding()
    }
 }

```

## Environmnet
- 모든 view가 읽을 수 있는 shared data. 
- 모든 뷰에서 접근 가능한 데이터 or 뷰에서 뷰로 전달하지 않아도 되는 데이터
- 선언 형식은 Observable과 동일하게 ObservableObject 주입받고 @Published를 사용해 데이터를 선언한다.
- 사용자 인터페이스 밖에 있으며 여러뷰가 접근해야하는 데이터를 갖을 경우
- 선언 형식은 Observable과 동일하게 ObservableObject 주입받고 @Published를 사용해 데이터를 선언한다.
- 뷰에서 전달 받을 경우 초기화 시점이 아닌 단순 주입형태
- **@EnvironemntObject var:** 뷰에서 binding할 property 

**사용예시**

```swift
  let environmentDemoData = EnvironmentDemoData()
  EnvironmentDemoView().environmentObject(environmentDemoData)
```

```swift
 class EnvironmentDemoData: ObservableObject {
    @Published var userCount = 0
    @Published var currentUser = String()
    
    init() {}
    
    func updateData() {}
 }

 struct EnvironmentDemoView: View {
    @EnvironmentObject var demoData: EnvironmentDemoData

    var body: some View {
        VStack {
            Text("\(demoData.currentUser), number \(demoData.userCount)")
        }
        .padding()
    }
 }
```

**도대체 뷰에서 뷰로 전달하지 않을 때만 사용하는 이유가 뭐야?!?!
심지어 같은 기능 Observable에서 똑같이 구현가능해! 그냥 init으로 주입안하는거 말고 도대체 뭐가 다른거지??**
