---
layout: post
title: wLog \#1 20190610
author: whyn4
categories: [ wlog, machine learning ]
image: assets/images/posts/wlog.png
comments: true
---

Dạo này tôi thường xuyên bị quên những thứ mà mình vừa học được, và đến khi cần lại tôi luôn mất một khoảng thời gian khá lâu với những keyword mà mình nhớ mang máng.

Đó là lý do tôi tạo nên wLog series này.

## 1. Machine learning:

Sau một thời gian thử nghiệm OpenCV trong bài toán Face recognition kết hợp với thuật toán [Local Binary Patterns Histogram (LBPH)](https://docs.opencv.org/2.4/modules/contrib/doc/facerec/facerec_tutorial.html) thì khi áp dụng vào thực tế model của tôi thường xuyên xử lý sai (nhận diện sai người), xác suất trả về unknown label cao.
Nên tôi bắt đầu mày mò nhiều hơn về mảng này:

<!--more-->

- https://khanh-personal.gitbook.io/ - Một cuốn ebook về những khái niệm trong Machine learning, tôi đã học được những khái niệm này trước đây, nhưng sau khi học thêm ở đây tôi có thêm 1 góc nhìn mới về Machine learning.
- https://en.wikipedia.org/wiki/Generative_adversarial_network - Như ở trên tôi gặp vấn đề với bài toán xác định unknown label, anomaly detection - xác định điểm bất thường
- Bên cạnh Facenet thì còn có thêm Insightface, VGG Face2, Dlib
- Numpy:
    - Các phiên bản của hàm Softmax
    - Stochastic Gradient Descent với hoán vị ngẫu nhiên
    - Mảng ngẫu nhiên phân phối chuẩn và phân phối đều
- https://bitbucket.org/dungnb1333/dnb-facerecognition-aivivn/src/master/ - 1st solution trong bài toán Face recognition trong cuộc thi do AIviVN tổ chức
- Hàm mục tiêu Objective function và regularizer
- Overfitting


## 2. Smart home:
- BRC301B611 - Tôi đang tìm mua board controller này của Daikin để tiếp tục tự động hóa máy thu hồi nhiệt Daikin Heat reclaim ventilator.
- Switchglass https://www.youtube.com/watch?v=WCzeuL7hNhk - Một loại kính dùng thay thế cho rèm, có thể chuyển từ trong suốt sang đục, được áp dụng trong phòng họp, phòng tắm, ...

## 3. Security:
- Tôi vô tình đọc được một phương thức tấn công - keyword là: speculative execution exploitation. Nhưng tôi sẽ cố gắng học thêm sau.

---
2019/06/10 23:00
