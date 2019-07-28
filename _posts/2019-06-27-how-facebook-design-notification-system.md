---
layout: post
title: Facebook đã thiết kế hệ thống Notification của họ như thế nào
author: whyn4
categories: [ wlog, system-design ]
image: assets/images/posts/wlog.png
comments: true
---

<image src="https://raw.githubusercontent.com/Quynh-Nguyen/quynh-nguyen.github.io/master/post-images/how-facebook-design-notification-system/elements.png" width=500 />

Bạn có bao giờ tự hỏi các Notification trên Facebook, Medium, Instagram, ... được thiết kế như thế nào chưa?
Liệu đây có phải là một việc hoàn toàn đơn giản giữa người gửi và người nhận?
Hay chỉ cần quan tâm đến vấn đề Realtime?

Đó là lý do của bài viết này, vì mình thật sự tin rằng để có một hệ thống Notification mạnh mẽ, bạn cần một thiết kế mạnh mẽ. Vậy yêu cầu của một hệ thống Notification mạnh mẽ là gì?

- Thiết kế cấu trúc cơ sở dữ liệu để lưu trữ thông tin của một notification.
- Làm thế nào để sinh ra một notification message dễ đọc hiểu từ những dữ liệu lưu trong cơ sở dữ liệu?
- Làm thế nào để hệ thống Activities sau này cũng có thể kế thừa từ hệ thống Notification?
- Vấn đề phân trang, load-more cho nhiều notification?
- Vấn đề real-time, hiệu năng?

Bài viết này có thể không cung cấp cụ thể đến mức cho bạn từng example code, là vì mình muốn tập trung vào một khía cạnh khác quan trọng hơn đó chính là thiết kế hệ thống.

<!--more-->

## 1. Những thách thức khi triển khai:

Trước khi đi vào phần thiết kế cấu trúc cơ sở dữ liệu và thực hiện, chúng ta hãy cùng đi qua những thách thức phải đối mặt khi thực hiện một hệ thống Notification:

### Tăng trưởng dữ liệu

Một trong những vấn đề muôn thuở chính là tốc độ tăng trưởng của dữ liệu, đặc biệt với một hệ thống Notification.

Bởi vì với từng hành động của người dùng, chúng ta lại cần lưu ít nhất là một record trong cơ sở dữ liệu. Cho nên để cải thiện vấn đề này chúng ta cần tránh việc lưu trùng lặp, chỉ lưu những thông tin cơ bản đủ để cấu thành nên một Notification message đẩy đủ nghĩa là được rồi.

### Nhiều người cùng nhận một thông báo

Một notification có thể được gửi cho một hoặc nhiều người cùng một lúc. Cho nên thiết kế cấu trúc cơ sở dữ liệu để có thể hạn chế lưu quá nhiều record cho trường hợp này.

### Nhiều loại thông báo

Tương tự như Facebook hay Medium chúng ta sẽ có rất nhiều loại thông báo khác nhau: thông báo bài viết, kết bạn, theo dõi, thông báo của group, ... Cho nên thiết kế cơ sở dữ liệu cũng phải đủ linh hoạt để xử lý và mở rộng sau này.

## 2. Một thông báo cần có những dữ liệu gì?

<image src="https://raw.githubusercontent.com/Quynh-Nguyen/quynh-nguyen.github.io/master/post-images/how-facebook-design-notification-system/Example-01.png" width=500 />

Với một notification bạn sẽ có 4 thành phần chính:

- Actor
- Receiver
- Entity
- Entity type

### Actor

<image src="https://raw.githubusercontent.com/Quynh-Nguyen/quynh-nguyen.github.io/master/post-images/how-facebook-design-notification-system/Actor.png" width=500 />

Actor là người kích hoạt một thông báo, bằng cách lưu định danh của user này dưới dạng `user_id` sau này chúng ta cũng có thể query để lấy Activities của một user.

Ví dụ nhé, nếu Alpha gửi lời mời kết bạn cho Beta, thì với thằng Beta sẽ nhận được một thông báo, nhưng với thằng Alpha mình sẽ đưa nó vào danh sách hoạt động (Activities).

### Receiver

<image src="https://raw.githubusercontent.com/Quynh-Nguyen/quynh-nguyen.github.io/master/post-images/how-facebook-design-notification-system/Receiver.png" width=500 />

Receiver là người nhận thông báo, một thông báo có thể gửi đến một hoặc nhiều người, vì vậy chúng ta sẽ xem xét lưu mảng `user_ids`, thế nhưng làm sao để xác định 1 người nhận trong mảng trên đã đọc thông báo nhỉ? Chúng ta sẽ xem xét tuỳ trường hợp mà lưu mảng `user_ids` hay lưu nhiều record nhé.

### Entity

<image src="https://raw.githubusercontent.com/Quynh-Nguyen/quynh-nguyen.github.io/master/post-images/how-facebook-design-notification-system/Entity.png" width=500 />

Entity tức là loại thông báo, nó giúp chúng ta phân biệt được đó là thông báo gì: về nhóm, một bài post, về một comment, hay một lời mời kết bạn chẳng hạn.

### Entity type

<image src="https://raw.githubusercontent.com/Quynh-Nguyen/quynh-nguyen.github.io/master/post-images/how-facebook-design-notification-system/Entity%20Type.png" width=500 />

Entity types là kiểu của một loại thông báo, nói văn hoa thì hơi khó hình dung, nên hãy đến với vài ví dụ cụ thể nhé:

Cùng là 1 Entity User nhưng bạn có đến 3 kiểu notification liên quan đến User:

1. User A tạo mới một user khác.
2. User A (quản trị viên) cập nhật thông tin của một user.
3. User A (quản trị viên) xoá đi một user.

Vậy đấy, nên để định danh một entity, một entity type ta cần `entity_id` và `entity_type_id`.

Kiểu như vậy nè:

| entity_id | entity_type_id | Description | Notification message |
| :-------: | :------------: | :---------- | :------------------: |
| 101       | 1              | Thông báo này được gửi khi tạo một user | User A created a new user. |
| 101       | 2              | Thông báo này được gửi khi cập nhật dữ liệu một user | User A updated the user B. |
| 101       | 3              | Thông báo này được gửi khi xoá một một user | User A deleted the user B. |
| 102       | 4              | Thông báo này được gửi khi user B comment vào bài viết của user A | User B commented on your post. |
| 102       | 5              | Thông báo này được gửi khi user B thích bài viết của user A | User A liked your post. |

Hy vọng đến đây bạn đã hình dung được những vấn đề và kiến trúc cơ sở dữ liệu của chúng ta như thế nào rồi nhé.

## Thiết kế cấu trúc cơ sở dữ liệu

<image src="https://raw.githubusercontent.com/Quynh-Nguyen/quynh-nguyen.github.io/master/post-images/how-facebook-design-notification-system/Notification_DB.png" width=500 />

## Thiết kế cấu trúc cơ sở dữ liệu để lưu chi tiết từng notification

Đến đây chúng ta cần móc nối các bảng với nhau, đồng thời cần có một bảng để có thể lưu trữ việc móc nối giữa các Actor, Receiver và các Entity, Entity Type.


### Table notification_objects

#### entity_type_id

Như đã nói hệ thống notification sẽ có rất nhiều kiểu thông báo khác nhau, với mỗi thông báo sẽ có một entity_type_id khác nhau để từ đó chúng ta có thể biết được nó đến từ hành động nào và làm sao sinh ra một thông báo đầy đủ.

Ví dụ nhé:

| entity_type_id | Entity    | Notification description |
| :------------- | :-------- | :----------------------- |
| 1              | Post      | Tạo một bài viết         |
| 2              | Post      | Cập nhật một bài viết    |
| 3              | Post      | Xoá một bài viết         |
| 4              | Message   | Gửi một bài viết         |

#### entity_id

`entity_id` giúp bạn xác định được đối tượng liên quan đến thông báo.
Cụ thể là, bạn có một `entity_type_id = 1` có nghĩa là tạo một bài viết mới, vậy `entity_id` chính là `id` của bài viết mới, liên kết trực tiếp đến bảng `posts`.

Một vài ví dụ cụ thể hơn về `entity_id`:

| entity_type_id | entity_id | Notification description                         |
| :------------- | :-------- | :----------------------------------------------- |
| 1              | 101       | Tạo một bài viết mới với `post_id` = 101         |
| 8              | 101       | Tạo một user mới với `user_id` = 101             |
| 1              | 103       | Tạo một bài viết mới với `post_id` = 103         |
| 2              | 103       | Cập nhật một bài viết với `post_id` = 103        |

### Table notification_change

Bảng này dùng để lưu trữ những thông tin của người tạo ra thông báo.

### notification_object_id

Đây là khoá ngoại tới bảng notification_objects để lấy chi tiết của thông báo.

### actor_id

Bạn có thể xem đây như là `user_id` người tạo ra thông báo

### Table notifications

Bảng `notifications` dùng để lưu trữ những thông tin của những người nhận được thông báo.

### notification_object_id

Đây là khoá ngoại tới bảng notification_objects để lấy chi tiết của thông báo.

### receiver_id

Bạn cũng có thể xem đây như là `user_id` của người nhận được thông báo, nếu cùng một thông báo gửi đến bao nhiêu người thì ở bảng này sẽ có bấy nhiêu records.


## Implementation

Đã xong phần thiết kế cấu trúc cơ sở dữ liệu, giờ hãy cùng thực hiện một vài ví dụ chi tiết thôi anh em.

Hãy tưởng tượng ở đây chúng ta có 3 users trong hệ thống

### Users

| user_id | username |
| :------ | :------- |
| 1       | Alpha    |
| 2       | Beta     |
| 3       | Nana     |

Và sẽ có 2 hành động sau đây được thực hiện

// Vẽ hình

Chúng ta sẽ cần lưu trữ những thông tin sau

1. Entity type id
2. Entity id
3. Actor id
4. Receiver id

Giả sử rằng chúng ta đã định nghĩa Entity types như sau:

| entity_type_id | entity_table | Notification description     |
| :------------- | :----------- | :--------------------------- |
| 1              | Posts        | Create new post              |
| 2              | Comments     | Create new comment on a post |

### Posts

| post_id | post_description | user_id | created_at          | status |
| :------ | :--------------- | :------ | :------------------ | :----: |
| 101     | .....            | 1       | 2019-07-11 14:12:13 | 1      |

### Comments

|

---
2019/06/26 02:00
