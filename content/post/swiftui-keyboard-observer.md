---
title: "SwiftUI 监听 keyboard 事件"
date: 2021-02-23T08:59:11+08:00
lastmod: 2021-02-28T07:53:11+08:00
draft: false
tags: ["ios", "swiftui"]
---

通过 `@ObservedObject` 进行依赖注入一个 keyboard 的处理类，监听系统的 `Notification`。

<!--more-->

```swift
final class KeyboardFollower: ObservableObject {
    @Published var keyboardHeight: CGFloat = 0
    @Published var isVisible: Bool = false

    func subscribe() {
        NotificationCenter.default.addObserver(self, selector: #selector(keyboardVisibilityChanged), name: UIResponder.keyboardWillChangeFrameNotification, object: nil)
    }

    func unsubscribe() {
        NotificationCenter.default.removeObserver(self, name: UIResponder.keyboardWillChangeFrameNotification, object: nil)
    }

    @objc func keyboardVisibilityChanged(_ notification: Notification) {
        guard let userInfo = notification.userInfo else { return }
        guard let keyboardBeginFrame = userInfo[UIResponder.keyboardFrameBeginUserInfoKey] as? CGRect else { return }
        guard let keyboardEndFrame = userInfo[UIResponder.keyboardFrameEndUserInfoKey] as? CGRect else { return }
        isVisible = keyboardBeginFrame.minY > keyboardEndFrame.minY
        keyboardHeight = isVisible ? keyboardEndFrame.height : 0
    }
}
```

在 View 初始化时注入一个 `KeyboardFollower` 的实例，监听 `KeyboardHandler` 中的变更：

```swift
struct RegisterView: View {
    @ObservedObject var keyboardHandler: KeyboardFollower
    @EnvironmentObject var userManager: UserManager

    var body: some View {
        ZStack {
            ...
            VStack {
                WelcomeMessageView()
                TextField("Name", text: $userManager.profile.name)
                    .bordered()
                    .padding([.leading, .trailing])
                ...
            }
            .padding(.bottom, keyboardHandler.keyboardHeight)
            .edgesIgnoringSafeArea(keyboardHandler.isVisible ? .bottom : [])
            .onAppear { self.keyboardHandler.subscribe() }
            .onDisappear { self.keyboardHandler.unsubscribe() }
        }
    }
}
```
