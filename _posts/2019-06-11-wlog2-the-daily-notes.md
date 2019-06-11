---
layout: post
title: wLog2 2019/06/11
author: whyn4
categories: [ wlog, esp8266 ]
image: assets/images/posts/wlog.png
comments: true
---

Dạo này tôi thường xuyên bị quên những thứ mà mình vừa học được, và đến khi cần lại tôi luôn mất một khoảng thời gian khá lâu với những keyword mà mình nhớ mang máng.

Đó là lý do tôi tạo nên wLog series này.

## 1. ESP8266 NodeMCU:

Hôm nay tôi tiếp tục làm remote controller cho máy thu hồi nhiệt Daikin Heat Reclaim Ventilator. Ý tưởng của tôi là dùng ESP8266 để connect đến MQTT Server và nhận command, sau đó sẽ kích Relay để giả lập nút bấm. Sau đây là 1 số ghi chú mà tôi học được hôm nay

<!--more-->

- Chân 5V: Khi cấp nguồn cho ESP8266 thì trên board bạn sẽ có 1 số áp ra như 3.3V, 5V.
    - Với các ESP8266 NodeMCU version 1 thì chân 5V là chân GU
    - Với các ESP8266 NodeMCU version mới thì chân 5V là chân VIN
    - Bạn có thể dùng đồng hồ đo trước khi làm
- Sản phẩm của tôi:
![Relay remote controller](/assets/images/posts/esp8266-relay-controller.jpg)


## 3. Livestream:
- Hôm nay, tôi và một số anh em phân tích Twitch, nền tảng Livestream cho các streamer trên thế giới và đặt câu hỏi vì sao Youtube Gaming, Facebook Gaming mãi là kẻ đi sau:
    - Hệ thống quản lý video, clip của Twitch rất bá đạo
    - Twitch cho phép Streamer customize giao diện Livestream của chính họ
    - M3U8 protocol: tôi nghĩ Youtube và Facebook cũng đang xài protocol tương tự

---
2019/06/12 02:00
