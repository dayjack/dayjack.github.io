---
layout: post
title:  "SwiftUI - Localization "
date:   2023-04-24 20:00:36 +0900
categories: Swift
---
**Localization이란?**

<aside>
💡 언어 현지화는 특정한 언어로 제작된 프로그램이나 제품을 다른 언어로 사용할 수 있게 하는 과정을 말한다.

</aside>

## Localization 적용하기

1. **프로젝트에 Localizable.string 추가**
    
    ![image1.png](/assets/image/20230424/image1.png)
    
    ![image2.png](/assets/image/20230424/image2.png)
    
2. **Localizable 인스펙터 탭에서 Localize 버튼 클릭**
    
    ![image3.png](/assets/image/20230424/image3.png)
    
    ![image4.png](/assets/image/20230424/image4.png)
    
3. **프로젝트 파일 눌러서 Localizations 의 +를 눌러서 Korean(ko) 클릭**
    
    ![image5.png](Lassets/image/20230424/image5.png)
    
    ![image6.png](/assets/image/20230424/image6.png)
    
4. **Localizable 파일 내용 작성**
    
    ```swift
    // "키" = "값" 형식
    "name" = "Eugene";
    // Int는 %lld로 불러올수 있음
    "age %lld" = "My age is %lld";
    // String은 %@로 불러올수 있음
    "lastname %@" = "My lastname is %@";
    ```
    
    ```swift
    "name" = "유진";
    "age %lld" = "제 나이는 %lld";
    "lastname %@" = "내 성은 %@ 입니다.";
    ```
    
5. **ContentView에서 텍스트 불러오기**
    
    ```swift
    import SwiftUI
    
    struct ContentView: View {
        
    		// "name" 불러오기 
        let name: LocalizedStringKey = "name"
        let age: Int = 27
        let lastname: String = "Choi"
        
        var body: some View {
            VStack {
                Text(name)
    						// "age %lld" 불러오기
                Text("age \(self.age)")
    						// "lastname %@" 불러오기
                Text("lastname \(self.lastname)")
            }
            .padding()
        }
    }
    
    struct ContentView_Previews: PreviewProvider {
        static var previews: some View {
            ContentView()
                .environment(\.locale, .init(identifier: "en"))
        }
    }
    ```
    
6. **결과**
    
    ![image9.png](/assets/image/20230424/image9.png)
    
    ![image10.png](/assets/image/20230424/image10.png)