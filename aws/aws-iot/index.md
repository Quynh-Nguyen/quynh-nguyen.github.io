---
layout: default
---
# Internet of things và hành trình với AWS IoT

Bài viết này sẽ bỏ qua các phần lý thuyết và định nghĩa dài dòng, thay vào đó chúng ta đặt ra một bài toán, và đi giải nó - bằng [**Amazon Web Services IoT (Internet of Things)**](https://aws.amazon.com/iot/).

## 1. Demo:

## 2. Đặt vấn đề:

> Một trường học muốn triển khai hệ thống liên kết với các bậc phụ huynh học sinh thông qua thiết bị camera, với các chức năng cụ thể:
> *   Mỗi buổi sáng khi học sinh đến trường sẽ nhìn vào Camera và nói: `Hi, I'm {Name}!`
> *   Camera sẽ nhận diện học sinh, chụp một tấm hình
> *   Tấm hình chụp sẽ được gửi cho Ban giám hiệu và phụ huynh học sinh (phụ huynh của học sinh nào thì sẽ nhận được hình của học sinh đó)

## 3. Giải pháp:

![AWS IoT Example](https://quynh-nguyen.github.io/aws/aws-iot/AWS%20IoT.png)

## 4. Xây dựng:

### 4.1. Camera Node:

Camera Node cần 2 chức năng chính:
1.  Speech Recognition - Nhận diện giọng nói
2.  Capture Picture - Chụp hình

#### 4.1.1 Speech Recognition:

```python
import speech_recognition as sr

# Record Audio
r = sr.Recognizer()

def main():
    audio = r.listen(source)
    # Speech recognition using Google Speech Recognition
    try:
        voiceRecognition = r.recognize_google(audio, language='vi-VN')
        print("You said: " + voiceRecognition)
        if "Xin chào" in voiceRecognition:
            print("Capture Picture")
    except sr.UnknownValueError:
        print("Google Speech Recognition could not understand audio")
    except sr.RequestError as e:
        print("Could not request results from Google Speech Recognition service; {0}".format(e))


with sr.Microphone() as source:
    print("Say something!")
    while True:
        main()
```

Để tăng thêm phần chuyên nghiệp cho Speech Recognition các bạn có thể thêm Wake-up keywork bằng `Pocketsphinx` package. [Tham khảo](https://stackoverflow.com/questions/25394329/python-voice-recognition-library-always-listen/25472887)

#### 4.1.2 Capture Picture:

Text can be **bold**, _italic_, ~~strikethrough~~ or `keyword`.

[Link to another page](https://kubernetes.io/).

There should be whitespace between paragraphs.

There should be whitespace between paragraphs. We recommend including a README, or a file with information about your project.

This is a normal paragraph following a header. GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere.

## Header 2

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.

### Header 3

```js
// Javascript code with syntax highlighting.
var fun = function lang(l) {
  dateformat.i18n = require('./lang/' + l)
  return true;
}
```

```ruby
# Ruby code with syntax highlighting
GitHubPages::Dependencies.gems.each do |gem, version|
  s.add_dependency(gem, "= #{version}")
end
```

#### Header 4

*   This is an unordered list following a header.
*   This is an unordered list following a header.
*   This is an unordered list following a header.

##### Header 5

1.  This is an ordered list following a header.
2.  This is an ordered list following a header.
3.  This is an ordered list following a header.

###### Header 6

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### There's a horizontal rule below this.

* * *

### Here is an unordered list:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### And an ordered list:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### And a nested list:

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### Small image

![Octocat](https://assets-cdn.github.com/images/icons/emoji/octocat.png)

### Large image

![Branching](https://guides.github.com/activities/hello-world/branching.png)


### Definition lists can be used with HTML syntax.

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```
