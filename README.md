# Stateful SwiftUI WebView for iOS and MacOS

Fully functional, SwiftUI-ready WebView for iOS 13+ and MacOS 10.15+. Actions and state are both delivered via SwiftUI `@Binding`s, meaking it dead-easy to integrate into any existing SwiftUI View.

![Preview iOS](https://github.com/globulus/swiftui-webview/blob/main/Images/preview_ios.gif?raw=true)

![Preview macOS](https://github.com/globulus/swiftui-webview/blob/main/Images/preview_macos.gif?raw=true)

## Installation

This component is distributed as a **Swift package**.

## Sample usage

 * The **action** binding is used to control the WebView - whichever action you want it to perform, just set the variable's value to it. Available actions:
   + `idle` - does nothing and can be used as the default value.
   + `load(URLRequest)` - loads the given request.
   + `loadHTML(String)` - loads custom HTML string.
   + `reload`
   + `goBack`
   + `goForward`
 * The **state** binding reports back the current state of the WebView. Available data:
   + `isLoading` - `true` if the WebView is currently loading a page.
   + `pageTitle` - the title of the currently loaded page, or `nil` if it can't be obtained.
   + `pageHTML` - the HTML code of the page content. Set `htmlInState: true` in `WebView` initializer to receive this update.
   + `error` - set if an error ocurred while loading the page, `nil` otherwise.
   + `canGoBack`
   + `canGoForward`
 * The optional **restrictedPages** array allows you to specify hosts which the web view won't load.
 * **htmlInState** dictates if the `state` update will contain `pageHTML`. This is disabled by default as it's a costly operation.

```swift
import SwiftUIWebView

struct WebViewTest: View {
    @State private var action = WebViewAction.idle
    @State private var state = WebViewState.empty
    @State private var address = "https://www.google.com"
    
    var body: some View {
        VStack {
            titleView
            navigationToolbar
            errorView
            Divider()
            WebView(action: $action,
                    state: $state,
                    restrictedPages: ["apple.com"])
            Spacer()
        }
    }
    
    private var titleView: some View {
        Text(state.pageTitle ?? "Load a page")
            .font(.system(size: 24))
    }
    
    private var navigationToolbar: some View {
        HStack(spacing: 10) {
            TextField("Address", text: $address)
            if state.isLoading {
                if #available(iOS 14, macOS 10.15, *) {
                    ProgressView()
                        .progressViewStyle(CircularProgressViewStyle())
                } else {
                    Text("Loading")
                }
            }
            Spacer()
            Button("Go") {
                if let url = URL(string: address) {
                    action = .load(URLRequest(url: url))
                }
            }
            Button(action: {
                action = .reload
            }) {
                Image(systemName: "arrow.counterclockwise")
                    .imageScale(.large)
            }
            if state.canGoBack {
                Button(action: {
                    action = .goBack
                }) {
                    Image(systemName: "chevron.left")
                        .imageScale(.large)
                }
            }
            if state.canGoForward {
                Button(action: {
                    action = .goForward
                }) {
                Image(systemName: "chevron.right")
                    .imageScale(.large)
                    
                }
            }
        }.padding()
    }
    
    private var errorView: some View {
        Group {
            if let error = state.error {
                Text(error.localizedDescription)
                    .foregroundColor(.red)
            }
        }
    }
}
```

## Recipe

For a more detailed description of the code, [visit this recipe](https://swiftuirecipes.com/blog/webview-in-swiftui). Check out [SwiftUIRecipes.com](https://swiftuirecipes.com) for more **SwiftUI recipes**!
