# SwiftUI-Study

## Contents
- [State](https://github.com/eujin811/SwiftUI-Study/blob/main/README.md#state)
  - 사용법
  - 다시 그려지는 시점. 
  - 예시
- [ObservableObject](https://github.com/eujin811/SwiftUI-Study/blob/main/README.md#observableObject)
- [Observed](https://github.com/eujin811/SwiftUI-Study/blob/main/README.md#observed)
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
전
<img width="259" alt="스크린샷 2022-12-31 오후 3 45 27" src="https://user-images.githubusercontent.com/53036267/210227242-c1371d9d-4d9e-4e8d-a78b-d3fbd9d92b3e.png">

후
<img width="204" alt="스크린샷 2022-12-31 오후 3 45 31" src="https://user-images.githubusercontent.com/53036267/210227388-cf5344f8-7353-4f54-9b5a-fb1dfb944552.png">


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

## ObservableObject
- 객체들을 비동기적으로 만들어주는 protocol로 각각의 데이터들이 관찰하고 받아 올 수 있게 해주는 역할.
- 데이터를 불러오는 식별자로 데이터를 저장할 수 있는 클래스와 함께 사용된다.
- 데이터를 내려줄때에는 @Published를 사용한다.
- 데이터를 받아오는 쪽에서는 **@ObservedObject** 혹은 **EnvironmentObject**을 사용해 view쪽에서 데이터를 바인딩한다.
  ```swift
    class Object: ObservableObject { 
     @Published var ~~
      ... 
     }
    
    class _View: View {
      @ObservedObject var
      @EnvironmentObject var
    }
  ```
  

## Observed
- ObservedObject 프로토콜을 따르는 형태
- 각각의 뷰들이 데이터를 전달받아야 할 때 표현한다.
- 변경되야 할 데이터들의 값을 모아 관리하는 역할
- 뷰에서 뷰로 전달되며 뷰에서는 초기화 상태에서 주입되어야 한다.
  - 상위뷰에서 하위 뷰로 전달하는 형식으로 or view에서 view로 전달해야할 때 사용한다.
- Observable 객체는 Published property로서 데이터 값을 게시(publish)한다. / 데이터 선언부
  - @Published var ~
- Observer 객체는 게시자를 구독(Subscribe)하여 published property가 변경될 때마다 업데이트 받는다. / view binding
  - @ObservedObject var ~

<img width="925" alt="ObservedObject" src="https://user-images.githubusercontent.com/53036267/210227434-a47a6e21-8241-49a3-a76e-9401427f60b8.png">

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
  - 혹은 중간 단계의 view를 생략하고 싶을 때 사용.
- 선언 형식은 Observable과 동일하게 ObservableObject 주입받고 @Published를 사용해 데이터를 선언한다.
- 사용자 인터페이스 밖에 있으며 여러뷰가 접근해야하는 데이터를 갖을 경우
- 선언 형식은 Observable과 동일하게 ObservableObject 주입받고 @Published를 사용해 데이터를 선언한다.
- 뷰에서 전달 받을 경우 초기화 시점이 아닌 단순 주입형태
- **@EnvironemntObject var:** 뷰에서 binding할 property 

<img width="945" alt="EnvironmentObject" src="https://user-images.githubusercontent.com/53036267/210227503-d93e2054-b682-422c-b9b2-0383df53c7ef.png">


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


**Observable 참고 예시**
- 어떤 상황에 사용해야 적합할까?라는 생각에 앞으로 쓰게될 TCA를 뒤적뒤적!
- tca에서는 store에서 기능을 작동하는데 View에서 store의 기능 받아올 때 **ObservedObject**사용!
  - store에서 내려주는 기능들 사용 + 내부 view들에 내려줄 수 있어야 하기 때문일것으로 추측!
  
  <img width="641" alt="tca_viewStore" src="https://user-images.githubusercontent.com/53036267/210234571-29135c3a-8b73-4230-94ab-f70ca3edd79d.png">
  
  <img width="657" alt="tca_withVieStore" src="https://user-images.githubusercontent.com/53036267/210234743-f734d434-8734-4901-a163-238232d6cb7c.png">



