---
layout: post
title:  "NavigationStack?"
date:   2023-04-30 12:00:36 +0900
categories: Swift SwiftUI
---

<aside>
💡 기존 화면 전환의 방식 NavigationView를 Stack 처럼 적용함
</aside>

## NavigationStack 기본 & Detail에서의 NavigationLink

> **기본적인 사용법**
> 

### 예제에 사용할 데이터 모델

```swift
import Foundation

struct Appcircle: Identifiable, Hashable {
    let id = UUID()
    let brand : String
    let price : Int
}

extension Appcircle {
    /// 더미 데이터를 반환하는 타입 프로퍼티
    static var dummyData: [Appcircle] {
        return [
            .init(brand: "iPhone 13", price: 799),
            .init(brand: "iPhone 4", price: 399),
            .init(brand: "iPhone 8", price: 599),
            .init(brand: "iPhone 11", price: 699),
            .init(brand: "iPhone 3", price: 299)
        ]
    }
}
```

### ContentView의 예제코드

```swift
import SwiftUI

struct ContentView: View {
    
    var body: some View {
        NavigationStack {
            // 더미 데이터를 사용하여 리스트 생성
            List(Appcircle.dummyData) { item in
                // .navigationDestination 와 같이 사용, value에 넘겨줄 데이터를 넣는다.
                NavigationLink(item.brand, value: item)
            }
            .listStyle(.plain)
            // NavigationLink 와 같이 사용, 받아온 데이터를 DetailView에 넘겨준다.
            .navigationDestination(for: Appcircle.self) { item in
                DetailView(item: item)
            }
            .navigationTitle("Navigation Stack")
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

### DetailView의 예제코드

```swift
import SwiftUI

struct DetailView: View {
    
    // ContentView에서 넘어온 데이터를 담는다.
    @State var item: Appcircle
    // DetailView에서 NavigationLink를 테스트하기 위한 더미 데이터
    @State var selectionData = [
        "Selection#1",
        "Selection#2",
        "Selection#3",
        "Selection#4"
    ]
    
    var body: some View {
        
        VStack {
            // 받아온 데이터를 표시
            Text(item.brand)
                .font(.largeTitle)
                .bold()
                .padding()
            Text("\(item.price)$")
            // DetailView에서 NavigationLink 테스트
            List {
                ForEach(selectionData, id: \.self) { data in
                    NavigationLink {
                        Text(data)
                    } label: {
                        Text(data)
                    }
                }
            }
        }
    }
}

struct DetailView_Previews: PreviewProvider {
    static var previews: some View {
        DetailView(item: .init(brand: "iPhone 17", price: 123456))
    }
}
```

## Enum을 사용한 NavigationStack 활용

> **Enum case에 따라 화면 자체가 아예 달라져야 할때 사용할 수 있음**
> 

### 사용할 데이터 모델

```swift
import Foundation

struct SchoolModel: Hashable, Identifiable  {
    
    var id = UUID()
    var state: School = .none
    
    enum School {
        case Primary
        case Middle
        case High
        case College
        case University
        case none
    }
}
```

### ContentView 예제

```swift
import SwiftUI

struct ContentView: View {
    
    // 사용할 더미 데이터
    @State var schoolArr: [SchoolModel] = [
        .init(state: .Primary),
        .init(state: .Middle),
        .init(state: .High),
        .init(state: .College),
        .init(state: .University),
        .init(state: .none)
    ]
    
    var body: some View {
        
        NavigationStack {
            // 리스트 목록 생성
            List(schoolArr) { item in
                NavigationLink("Click me!!", value: item)
            }
            .navigationDestination(for: SchoolModel.self) { item in
                // 넣는 데이터만 다르게 보여는게 아니라 아예 다른 뷰로 대체 가능
                switch item.state {
                case .Primary:
                    Text("Primary")
                case .Middle:
                    Text("Middle")
                case .High:
                    Text("High")
                case .College:
                    Text("College")
                case .University:
                    Text("University")
                case .none:
                    Text("none")
                }
            }
            .navigationTitle("Enum 사용")
        }
        
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

## NavigationStack & path

> **이전 스택들의 화면 순서를 조작하거나 할 수 있음**
> 

### NavigationStack & path를 사용하는 예제 path의 순서를 조작하여 화면 순서를 조정할 수 있다.

```swift
//
//  ContentView.swift
//  PathNavigationStack
//
//  Created by ChoiYujin on 2023/04/30.
//

import SwiftUI

struct ContentView: View {
    
    /*
     path를 사용하면 Stack에 있는 View를 맘대로 순서를 바꾸거나 제거할 수 있다.
     */
    @State var path: [AppleProduct] = []
    
    @State var productArr: [AppleProduct] = [
        .init(name: "iPhone", price: 123),
        .init(name: "iPad", price: 234),
        .init(name: "iPod", price: 345),
        .init(name: "appleTV", price: 456),
        .init(name: "airpod", price: 567),
        .init(name: "magic mouse", price: 678)
    ]
    
    func removeAllPath() {
        self.path.removeAll()
    }
    
    var body: some View {
        
        /*
         path를 사용한 NavigationStack (Binding 변수)
         NavigationStack은 path State 변수를 관찰하게 된다.
         */
        NavigationStack(path: $path) {
            List(productArr) { item in
                NavigationLink(item.name, value: item)
            }
            .navigationDestination(for: AppleProduct.self) { item in
                VStack {
                    Text("\(item.name)'s price is \(item.price)$")
                    Text("path count: \(path.count)")
                    
                    Button {
                        /*
                         path 배열에 productArr에서 랜덤한 요소를 추가한다.
                         path가 늘어날수록 depth가 늘어남
                         뒤로 가기를 누르면 path의 마지막 요소는 삭제되고 그전에 있던 요소가 보임
                         */
                        path.append(productArr.randomElement()!)
                    } label: {
                        Text("Tab to append path!")
                    }
                    Button {
                        // path의 요소를 전부 삭제하면 root로 가게됨
                        removeAllPath()
                    } label: {
                        Text("Pop to root")
                    }

                }
            }
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

struct AppleProduct: Identifiable, Hashable {
    
    var id = UUID()
    var name: String
    var price: Int
}
```