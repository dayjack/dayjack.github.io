---
layout: post
title:  "Introduction to UI Testing in SwiftUI Using XCTest Framework"
date:   2023-04-20 22:00:36 +0900
categories: Swift
---

<!-- # Introduction to UI Testing in SwiftUI Using XCTest Framework -->

# **테스트에 대해서 알아보기**

![image.png](/assets/image/20230420/image.png)

# 튜토리얼

- Include Tests (자동으로 파일들을 만들어 준다.)

![image1.png](/assets/image/20230420/image1.png)

- UITestingDemoTest 및 UITestingDemoUITests 두 그룹이 있음
- 첫번째 폴더에는 단위테스트를 작성하는 파일이 포함되어 있다.
- 두번째 폴더의 파일에서 UI테스트를 작성할 것임

![image2.png](/assets/image/20230420/image2.png)

```swift
import SwiftUI

struct ContentView: View {
    
    @State private var showLogin = false
    
    var body: some View {
        VStack {
            Text("Welcome!")
                .font(.title)
     
            Spacer().frame(height: 20)
     
            Button(action: {
                showLogin = true
            }, label: {
                Text("Login")
            })
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

![image3.png](/assets/image/20230420/image3.png)

첫번째 테스트 작성

```swift
import XCTest

final class UITestingDemoUITests: XCTestCase {

    override func setUpWithError() throws {
        // Put setup code here. This method is called before the invocation of each test method in the class.

        // In UI tests it is usually best to stop immediately when a failure occurs.
        continueAfterFailure = false

        // In UI tests it’s important to set the initial state - such as interface orientation - required for your tests before they run. The setUp method is a good place to do this.
    }

    override func tearDownWithError() throws {
        // Put teardown code here. This method is called after the invocation of each test method in the class.
    }

    func testExample() throws {
        // UI tests must launch the application that they test.
        let app = XCUIApplication()
        app.launch()

        // Use XCTAssert and related functions to verify your tests produce the correct results.
    }

    func testLaunchPerformance() throws {
        if #available(macOS 10.15, iOS 13.0, tvOS 13.0, watchOS 7.0, *) {
            // This measures how long it takes to launch your application.
            measure(metrics: [XCTApplicationLaunchMetric()]) {
                XCUIApplication().launch()
            }
        }
    }
}
```

- XCTest 프레임워크는 테스트 작성에 필요한 프로그래밍 도구를 제공하는 프레임 워크
- Test는 실제로 XCTestCase 에서 상속되는 클래스의 작은 메서드
- UITestDemoUITests 클래스에는 몇 가지 미리 정의된 메서드가 있다.
    - setUpWithError() : 테스트를 실행하기 전에 호출되며 이전 메서드에서 생성된 리소스 또는 인스턴스를 해제하는 코드를 추가하는 곳입니다.
    - tearDownWithError() : 테스트 실행이 완료된 후에 호출되며, 이전 메서드에서 생성된 리소스 또는 인스턴스를 해체하는 코드를 추가하는 곳 입니다.
    - testExample() : 실증 테스트 방법입니다. “test” 키워드로 시작하는 메서드는 테스트로 간주됩니다.
    - testLaunchPerformance() : UI의 성능을 측정하는 방법입니다.

**레이블 테스트**

- 클래스 내부에 테스트 메서드 추가

```swift
func testWelcome() throws {
        // XCUIApplication 개체를 초기화
        // 이를 통해 모든 UI 요소에 엑세스할 것
        // 앱 인스턴스를 시작하고 UI테스트를 실행하기 위해 이를 사용할 것
    let app = XCUIApplication() 
        // 앱 인스턴스를 시작
    app.launch()
        // 실행중인 앱 인스턴스의 모든 정적 테스트에 엑세스할 수 있다.
        // "Welcome!" content가 있는 것을 요청
            // 반환된 값은 시작 상수에 할당된 XCUIelement object
    let welcome = app.staticTexts["Welcome!"]

        // 현재 ContentView에 텍스트뷰가 하나밖에 없으므로 이렇게도 접근이 가능하긴 하다.
    // let welcome = app.staticTexts.element

        // 기본요소에 대한 assertion을 만듬
        // 이전 줄에 지정된 content가 있는 텍스트뷰가 존재할 것이라고 생각
    XCTAssert(welcome.exists)
        
        //인수로 지정된 두 값이 같은지 테스트
        // 두 어설션이 모두 참이면 테스트가 통과된다. 
        XCTAssertEqual(welcome.label, "Welcome!")
}
```

- 메서드 이름 옆에 있는 마름모 꼴 아이콘을 클릭하여 테스트
    - 테스트는 성공한다.
    - 첫번째 assertion에서 느낌표를 제거하고 테스트하면 실패한다.
    
    ![image4.png](/assets/image/20230420/image4.png)
    

**로그인 버튼 테스트**

- 로그인 버튼이 있는지 확인하는 테스트를 작성한다.

```swift

func testLoginButton() throws {
    let app = XCUIApplication()
    app.launch()
 
    let login = app.buttons["Login"]
 
    XCTAssert(login.exists)
}
```

- 현재 상태로는 테스트 성공을 의심할 여지 없는 간단한 테스트이지만 미래에도 상황이 동일하게 유지된다는 보장이 없음
    - 버튼이 텍스트가 이미지로 바뀐다거나 이미지와 텍스트 둘 다로 바뀌거나 내용이 변경될 수 있음
- 버튼 레이블에서 발생할 수 있는 변경사항의 영향을 받지 않고 보다 강력하게 테스트하기 위해
- 버튼에 명시적인 접근성 식별자를 설정 하고 배열의 버튼에 액세스하기 위해 이를 아래 첨자로 사용할 수 있습니다

> **참고:** UI 테스트는 접근성을 기반으로 하므로 앱에 접근성 기능을 더 많이 추가할수록 UI 테스트를 더 쉽게 작성할 수 있습니다.
> 

- ContentView.swift 파일을 열고 버튼 구현 바로 뒤에 다음 줄을 추가합니다.

```swift

Button(
    ...
)
.accessibilityIdentifier("loginButton")
```

- 이제 레이블 대신 ‘loginButton’ 식별자를 사용하여 버튼을 고유하게 식별 할 수 있음
    - 이를 확인하기 위해 UITestingDemoUITests.swift 파일로 돌아가서 다음을 대체
        - 버튼의 제목을 변경해도 테스트는 성공적으로 유지됨
    
    ```swift
    // let login = app.buttons["Login"]
    let login = app.buttons["loginButton"]
    ```
    

### A Global XCUIApplication Objects

**위의 두 테스트는 모두 동일한 두줄로 시작되었다.**

```swift

let app = XCUIApplication()
app.launch()
```

- 모든 단일 테스트에서 반복되는 것을 피하기 위해 몇가지 변경을 수행
- 클래스의 모든 테스트에서 접근할 수 있게 app을 UITestingDemoUITests class 의 프로퍼티로 정의

```swift
class UITestDemoUITests: XCTestCase {
    let app = XCUIApplication()
 
    ...
}
```

- setUpWithError()에서 app.launch()

```swift
override func setUpWithError() throws {
        continueAfterFailure = false
        app.launch()
    }
```

## 로그인 양식

- 사용자 관련 데이터를 저장하는 간단한 모델을 구현해 봄
    - User.swift 파일 생성
    
    ```swift
    import Foundation
    import Combine
    
    class User: ObservableObject {
        @Published var isLoggedIn = false
        @Published var username = ""
        @Published var password = ""
     
        func login() -> Bool {
            guard username == "test" && password == "pass" else {
                return false
            }
     
            password = ""
            isLoggedIn = true
            return true
        }
    }
    ```
    
- 앱 전체에서 클래스의 인스턴스를 사용할 수 있기를 원하므로 SwiftUI 뷰의 환경에 추가해야 한다.
- UITestingDemoApp에 인스턴스로 추가

```swift
@main
struct UITestDemoApp: App {
    var user = User()
 
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(user)
        }
    }
}
```

- 간단한 로그인을 구현하기위해
    - 사용자 이름을 입력하는 textfield
    - 암호를 입력하는 securetextfield
    - (가짜) 로그인을 수행하는 로그인 button
    - 로그인 프로세스를 취소하는 닫기 button

```swift
import SwiftUI

struct LoginView: View {
    
    @EnvironmentObject private var user: User
    @Environment(\.presentationMode) var presentationMode
    
    var body: some View {
        NavigationView {
               Form {
                   Section {
                       TextField("Username", text: $user.username)
                       SecureField("Password", text: $user.password)
                   }
        
                   Button {
                       if user.login() {
                           presentationMode.wrappedValue.dismiss()
                       }
                   } label: {
                       Text("Login")
                   }
                   .accessibilityIdentifier("loginNow")
               }
               .navigationTitle(Text("Login"))
               .navigationBarItems(trailing: Button {
                   presentationMode.wrappedValue.dismiss()
               } label: {
                   Image(systemName: "xmark.circle")
                       .accessibilityLabel("Dismiss")
               })
           }
    }
}

struct LoginView_Previews: PreviewProvider {
    static var previews: some View {
        LoginView()
    }
}
```

```swift

var body: some View {
    VStack {
        ...
    }
    .sheet(isPresented: $showLogin) {
        LoginView()
    }
}
```

![image5.png](/assets/image/20230420/image5.png)

## 로그인 양식 테스트

- ContentView의 login button tab gesture trigger를 테스트
    - doubleTab()
    - swipeUp()
    - swipeDown()
    - pinch(withScale:velocity)
    - etc…
    - 여러가지가 가능하다.

```swift
func testLoginFormAppearance() throws {
    app.buttons["loginButton"].tap()
}
```

- UI요소갸 특정 기간후에 사용가능하게 되는 것을 기대하는 방법
- 예제를 위해 0.5초 기다림, 그렇지 않을경우 테스트가 실패

```swift
func testLoginFormAppearance() throws {
    app.buttons["loginButton"].tap()
        let loginNavBarTitle = app.staticTexts["Login"]
    XCTAssert(loginNavBarTitle.waitForExistence(timeout: 0.5))
}
```

## 로그인 양식의 모든 요소에 대한 테스트

```swift
func testLoginForm() throws {
    app.buttons["Login"].tap()
 
    let navBar = app.navigationBars.element
    XCTAssert(navBar.exists)
 
    let username = app.textFields["Username"]
    XCTAssert(username.exists)
 
    let password = app.secureTextFields["Password"]
    XCTAssert(password.exists)
 
    let login = app.buttons["loginNow"]
    XCTAssert(login.exists)
    XCTAssertEqual(login.label, "Login")
 
    let dismiss = app.buttons["Dismiss"]
    XCTAssert(dismiss.exists)
}
```

## 닫기 버튼 테스트

```swift

func testLoginDismiss() throws {
    app.buttons["Login"].tap()
    let dismiss = app.buttons["Dismiss"]
    dismiss.tap()
    XCTAssertFalse(dismiss.waitForExistence(timeout: 0.5))
}
```

## 사용자 이름 및 비밀번호 테스트

- 텍스트 필드에 텍스트를 입력하는 방법

```swift
func testUsername() throws {
        app.buttons["Login"].tap()
     
        let username = app.textFields["Username"]

                // tab gesture로 first responder로 만든다.
        username.tap()
                
                // typeText로 타이핑 프로세스를 시뮬레이트
        username.typeText("test")
     
        XCTAssertNotEqual(username.value as! String, "")
 }
```

- 비밀번호 입력을 위해 비슷한 방법으로 테스트
    - 시뮬레이터에서 테스트하는 경우 secureTextFields가 first responder가 될때 실제로 표시되는지 확인한다
    - 그렇지 않을 경우 Cmd + K 로 키보드를 띄워줌

```swift

func testPassword() throws {
    app.buttons["Login"].tap()
 
    app.secureTextFields.element.tap()
    app.keys["p"].tap()
    app.keys["a"].tap()
    app.keys["s"].tap()
    app.keys["s"].tap()
    app.keyboards.buttons["Return"].tap()
 
    XCTAssertNotEqual(app.secureTextFields.element.value as! String, "")
}
```

## 테스트 로그인

- 로그인 프로세스를 테스트

```swift

func testLogin() throws {
    app.buttons["Login"].tap()
 
    app.textFields.element.tap()
    app.textFields.element.typeText("test")
 
    app.secureTextFields.element.tap()
    app.secureTextFields.element.typeText("pass")
    app.keyboards.buttons["Return"].tap()
 
    let loginButton = app.buttons["loginNow"]
    loginButton.tap()
 
    XCTAssertFalse(loginButton.waitForExistence(timeout: 0.5))
}
```

## 테스트 실패 로그인

```swift
import SwiftUI

struct LoginView: View {
    
    @EnvironmentObject private var user: User
    @Environment(\.presentationMode) var presentationMode
    @State private var showAlert = false
    
    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Username", text: $user.username)
                    SecureField("Password", text: $user.password)
                }
                
                Button {
                    if user.login() {
                        presentationMode.wrappedValue.dismiss()
                    } else {
                        showAlert = true
                    }
                } label: {
                    Text("Login")
                }
                .accessibilityIdentifier("loginNow")
            }
            .navigationTitle(Text("Login"))
            .navigationBarItems(trailing: Button {
                presentationMode.wrappedValue.dismiss()
            } label: {
                Image(systemName: "xmark.circle")
                    .accessibilityLabel("Dismiss")
            })
        }
        .alert(isPresented: $showAlert) {
            Alert(title: Text("Login Failed"),
                  message: Text("Either username or password is missing, or they are wrong."),
                  dismissButton: Alert.Button.default(Text("OK"), action: {
                showAlert = false
            }))
        }
    }
}

struct LoginView_Previews: PreviewProvider {
    static var previews: some View {
        LoginView()
    }
}
```

- alert와 상호작용하고 확인버튼을 프로그래밍 방식으로 탭하여 경고를 해제한다.

```swift

func testFailedLoginAlert() throws {
    app.buttons["Login"].tap()
    app.buttons["loginNow"].tap()
 
    XCTAssert(app.alerts.element.waitForExistence(timeout: 0.5))
 
    app.alerts.element.buttons["OK"].tap()
    XCTAssertFalse(app.alerts.element.exists)
}
```

## 로그인 성공 후 ContentView 업데이트

1. “Welcome!” 메세지가 “Welcome test!”로 변경된다.
2. 버튼의 “login” 텍스트가 “logout”으로 업데이트되고 결국 로그아웃을 트리거한다.
3. 몇 가지 추가 UI 요소를 테스트하기 위해 다양한 임의 설정으로 양식을 표시한다.

```swift
import SwiftUI

struct ContentView: View {
    
    @State private var showLogin = false
    
    @EnvironmentObject private var user: User
    
    var body: some View {
        VStack {
            Text(!user.isLoggedIn ? "Welcome!" : "Welcome \(user.username)!")
                .font(.title)
            
            Spacer().frame(height: 20)
            
            Button(action: {
                if !user.isLoggedIn {
                    showLogin = true
                } else {
                    user.logout()
                }
            }, label: {
                Text(!user.isLoggedIn ? "Login" : "Logout")
            })
            .accessibilityIdentifier("loginButton")
        }
        .sheet(isPresented: $showLogin) {
            LoginView()
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

## 시작 및 로그인 버튼 레이블 테스트

```swift

func login() throws {
    app.buttons["Login"].tap()
 
    app.textFields.element.tap()
    app.textFields.element.typeText("test")
 
    app.secureTextFields.element.tap()
    app.secureTextFields.element.typeText("pass")
    app.keyboards.buttons["Return"].tap()
 
    app.buttons["loginNow"].tap()
}

func testWelcomeAfterLogin() throws {
    XCTAssert(app.staticTexts["Welcome!"].exists)
 
    try login()
 
    XCTAssert(app.staticTexts["Welcome test!"].exists)
}
```

## 테스트 로그아웃

```swift
func testLogout() throws {
    try login()
 
    XCTAssert(app.staticTexts["Welcome test!"].exists)
    XCTAssertEqual(app.buttons["loginButton"].label, "Logout")
 
    app.buttons["loginButton"].tap()
 
    XCTAssert(app.staticTexts["Welcome!"].exists)
    XCTAssertEqual(app.buttons["loginButton"].label, "Login")
}
```

## 로그인한 사용자를 위한 가짜 설정 추가

```swift
class User: ObservableObject {
    ...
 
    @Published var colorTheme = 0
    @Published var textSize: Double = 14
    @Published var font = "Arial"
 
    ...
}
```

```swift
struct ContentView: View {
    
    @State private var showLogin = false
    
    @EnvironmentObject private var user: User
    
    var body: some View {
        VStack {
            Text(!user.isLoggedIn ? "Welcome!" : "Welcome \(user.username)!")
                .font(.title)
            
            Spacer().frame(height: 20)
            
            Button(action: {
                if !user.isLoggedIn {
                    showLogin = true
                } else {
                    user.logout()
                }
            }, label: {
                Text(!user.isLoggedIn ? "Login" : "Logout")
            })
            .accessibilityIdentifier("loginButton")
            
            
            if user.isLoggedIn {
                Form {
                    Section {
                        VStack {
                            Text("Color Theme")
                            Picker("", selection: $user.colorTheme) {
                                Text("Light").tag(0)
                                Text("Dark").tag(1)
                            }
                            .pickerStyle(SegmentedPickerStyle())
                            .accessibilityIdentifier("colorTheme")
                        }
                    }
                    Section {
                        HStack {
                            Text("Text Size")
                            Slider(value: $user.textSize, in: 1...100)
                                .accessibilityIdentifier("slider")
                        }
                    }
                    Section {
                        VStack {
                            Text("Font")
                            Picker("", selection: $user.font) {
                                Text("Arial").tag("Arial")
                                Text("Avenir Next").tag("Avenir Next")
                                Text("Noteworthy").tag("Noteworthy")
                                Text("Futura").tag("Futura")
                            }
                            .pickerStyle(WheelPickerStyle())
                            .accessibilityIdentifier("fontPicker")
                        }
                    }
                    
                }
            }
        }
        .sheet(isPresented: $showLogin) {
            LoginView()
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

```swift
// 세그먼트 제어 테스트
func testColorTheme() throws {
    try login()
        
    let colorTheme = app.segmentedControls["colorTheme"]
    XCTAssert(colorTheme.exists)
        XCTAssert(colorTheme.buttons["Light"].isSelected)
        
    colorTheme.buttons["Dark"].tap()
    XCTAssert(colorTheme.buttons["Dark"].isSelected)
}

// 슬라이더 테스트
func testTextSize() throws {
    try login()
        
    let textSize = app.sliders["slider"]
    XCTAssert(textSize.exists)
        
    textSize.adjust(toNormalizedSliderPosition: 0.75)
    XCTAssertGreaterThanOrEqual(textSize.value as! String, "0.7")
}
    
// 휠 피커 테스트
func testFontPicker() throws {
    try login()
        
    let wheel = app.pickerWheels.element
    XCTAssert(wheel.exists)
    XCTAssertEqual(wheel.value as! String, "Arial")
        
    wheel.adjust(toPickerWheelValue: "Futura")
    XCTAssertEqual(wheel.value as! String, "Futura")
}

```

[GitHub - dayjack/UITestingDemo: Test Tutorial Code](https://github.com/dayjack/UITestingDemo)

전체 코드 보기
