<img src="Assets/Top.png" width="300" alt="Header image">

**Compose beautiful preference panels.**

- Simple but powerful syntax (powered by [result builders](https://www.hackingwithswift.com/swift/5.4/result-builders)).
- Create nested pages and groups.
- Fully searchable.
- Native integration with SwiftUI and AppStorage.
- Comes with pre-made components: Toggle, Button, Slider, etc...
- Style components with native SwiftUI modifiers.
- Show and hide components dynamically.
- Add your own custom SwiftUI views.
- Works on iOS and macOS.

![Screenshots of views created with Setting](Assets/Setting.png)

![Screenshots of a nested page and search results](Assets/Details.png)

### Installation

Setting is available via the [Swift Package Manager](https://developer.apple.com/documentation/swift_packages/adding_package_dependencies_to_your_app). Requires iOS 15+ or macOS Monterey and up.

```
https://github.com/aheze/Setting
```

### Usage

```swift
import DGSetting
import SwiftUI

struct PlaygroundView: View {
    /// Setting supports `@State`, `@AppStorage`, `@Published`, and more!
    @AppStorage("isOn") var isOn = true

    var body: some View {
        /// Start things off with `SettingStack`.
        SettingStack {
            /// This is the main settings page.
            SettingPage(title: "Playground") {
                /// Use groups to group components together.
                SettingGroup(header: "Main Group") {
                    /// Use any of the pre-made components...
                    SettingToggle(title: "This value is persisted!", isOn: $isOn)

                    /// ...or define your own ones!
                    SettingCustomView {
                        Image("Logo")
                            .resizable()
                            .aspectRatio(contentMode: .fit)
                            .frame(width: 160)
                            .padding(20)
                    }

                    /// Nest `SettingPage` inside other `SettingPage`s!
                    SettingPage(title: "Advanced Settings") {
                        SettingText(title: "I show up on the next page!")
                    }
                }
            }
        }
    }
}
```
![The result, a generated settings page. Clicking on "Advanced Settings" presents a new page.](Assets/Customizable.png)


### Examples

View more examples in the [example app](https://github.com/aheze/Setting/tree/main/Example/SettingExample).

<table>
<tr>
<td>

```swift
struct PlaygroundView: View {
    var body: some View {
        SettingStack {
            SettingPage(title: "Playground") {
                SettingGroup {
                    SettingText(title: "Hello!")
                }
            }
        }
    }
}
```
</td>
<td>

![Settings view rendered with "Hello!" label](Assets/1.png)

</td>
</tr>
</table>


<table>
<tr>
<td>

```swift
SettingStack {
    SettingPage(title: "Playground") {
        SettingGroup {
            SettingText(title: "Hello!")
        }

        SettingGroup {
            SettingPage(title: "First Page") {}
                .previewIcon("star")

            SettingPage(title: "Second Page") {}
                .previewIcon("sparkles")

            SettingPage(title: "Third Page") {}
                .previewIcon("leaf.fill")
        }
    }
}
```
</td>
<td>

![Settings view rendered with 3 row links](Assets/2.png)

</td>
</tr>
</table>


<table>
<tr>
<td>

```swift
struct PlaygroundView: View {
    @AppStorage("isOn") var isOn = true
    @AppStorage("value") var value = Double(5)

    var body: some View {
        SettingStack {
            SettingPage(title: "Playground") {
                SettingGroup {
                    SettingToggle(title: "On", isOn: $isOn)
                }

                SettingGroup(header: "Slider") {
                    SettingSlider(
                        value: $value,
                        range: 0 ... 10
                    )
                }
            }
        }
    }
}
```
</td>
<td>

![Settings view rendered with toggle and slider](Assets/3.png)

</td>
</tr>
</table>

<table>
<tr>
<td>

```swift
struct PlaygroundView: View {
    @AppStorage("index") var index = 0

    var body: some View {
        SettingStack {
            SettingPage(title: "Playground") {
                SettingGroup {
                    SettingPicker(
                        title: "Picker",
                        choices: ["A", "B", "C", "D"],
                        selectedIndex: $index
                    )
                    SettingPicker(
                        title: "Picker with menu",
                        choices: ["A", "B", "C", "D"],
                        selectedIndex: $index,
                        choicesConfiguration: .init(
                            pickerDisplayMode: .menu
                        )
                    )
                }
            }
        }
    }
}
```
</td>
<td>

![Settings view rendered with picker](Assets/4.png)

</td>
</tr>
</table>


<table>
<tr>
<td>

```swift
SettingStack {
    SettingPage(title: "Playground") {
        SettingCustomView {
            Color.blue
                .opacity(0.1)
                .cornerRadius(12)
                .overlay {
                    Text("Put anything here!")
                        .foregroundColor(.blue)
                        .font(.title.bold())
                }
                .frame(height: 150)
                .padding(.horizontal, 16)
        }
    }
}
```
</td>
<td>

![Settings view rendered with "Put anything here!" label](Assets/5.png)

</td>
</tr>
</table>

### Notes

- If multiple components have the same title, use the `id` parameter to make sure everything gets rendered correctly.

```swift
SettingText(id: "Announcement 1", title: "Hello!")
SettingText(id: "Announcement 2", title: "Hello!")
```

- Setting comes with `if-else` support!

```swift
SettingToggle(title: "Turn on", isOn: $isOn)

if isOn {
    SettingText("I'm turned on!")
}
```

- Wrap components in `SettingCustomView` to style them.

```swift
SettingCustomView {
    SettingText(title: "I'm bold!")
        .bold()
}
```

- Want to split up a Setting into multiple variables/files? Just use `@SettingBuilder`.

```swift
struct ContentView: View {
    var body: some View {
        SettingStack {
            SettingPage(title: "Settings") {
                general
                misc
            }
        }
    }
    
    @SettingBuilder var general: some Setting {
        SettingPage(title: "General") {
            SettingText(title: "General Settings")
        }
    }
    
    @SettingBuilder var misc: some Setting {
        SettingPage(title: "Misc") {
            SettingText(title: "Misc Settings")
        }
    }
}
```

- Need to store custom structs in `AppStorage`? Check out @IanKeen's awesome [gist](https://gist.github.com/IanKeen/4d29b48519dca125b21675eeb7623d60)!

- You can pass in a custom `SettingViewModel` instance for finer control.

<table>
<tr>
<td>

```swift
struct PlaygroundView: View {
    @StateObject var settingViewModel = SettingViewModel()

    var body: some View {
        SettingStack(settingViewModel: settingViewModel) {
            SettingPage(title: "Playground") {
                SettingGroup {
                    SettingText(title: "Welcome to Setting!")
                }
            }
        } customNoResultsView: {
            VStack(spacing: 20) {
                Image(systemName: "xmark")
                    .font(.largeTitle)

                Text("No results for '\(settingViewModel.searchText)'")
            }
            .frame(maxWidth: .infinity, maxHeight: .infinity)
        }
    }
}
```
</td>
<td>

![Settings view rendered with "Put anything here!" label](Assets/NoResults.png)

</td>
</tr>
</table>





---

### Community

Author | Contributing | Need Help?
--- | --- | ---
Setting is made by [aheze](https://github.com/aheze). | All contributions are welcome. Just [fork](https://github.com/aheze/Setting/fork) the repo, then make a pull request. | Open an [issue](https://github.com/aheze/Setting/issues) or join the [Discord server](https://discord.com/invite/Pmq8fYcus2). You can also ping me on [Twitter](https://twitter.com/aheze0).

### License

```
MIT License

Copyright (c) 2023 A. Zheng

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
