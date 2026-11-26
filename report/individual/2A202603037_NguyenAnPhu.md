# Báo cáo Day 13 — Nguyễn An Phú, 2A202603037

## 1. Kết luận trước

- MSSV: `2A202603037`; task: `D13-2A202603037`; số frame: 10.
- Hệ điều hành: Windows; trình duyệt: Brave Browser; phiên bản CVAT: 2.82.1.
- MSSV bạn ngồi cạnh: Chưa có thông tin.

Tôi đã gán 85 cuboid. Ít nhất là 5 cuboid ở các frame `s02f46`, `s04f42`, `s05f38`, đúng mức tối thiểu; có frame ít vật thể đủ rõ theo ảnh thực tế hoặc point cloud quá thưa để nội suy kích thước. Phiếu tự kiểm hiện có cảnh báo ở 83/85 cuboid; phần lặp lại nhiều nhất là `length < width` và kích thước dài/rộng ngoài khoảng. Bảng A cho thấy các chiều dài/rộng bị đảo có tính hệ thống, nên tôi sẽ đối chiếu trong CVAT và báo coach thay vì sửa CSV bằng tay.

Từ bằng chứng hiện có, phần cần rà kỹ nhất là hướng yaw và chiều dài/rộng; phần tôi tự tin nhất: chưa tự xác định.

**Cách dùng CVAT**
1. Xoay khung 3D bằng chuột trái; dời bằng chuột phải; phóng to/thu nhỏ bằng con lăn. Nhìn từ trên xuống bằng cách xoay khung, rồi nháy đúp chỗ trống để về góc nhìn mặc định.
2. Chọn **Shape**, chọn class, đặt cuboid bằng nháy đúp; chỉnh tâm, dài/rộng và yaw ở **Top**, chỉnh cao độ ở **Side/Front**.
3. Bật **Appearance → Cuboid orientation** để kiểm tra hướng; đối chiếu ảnh camera và bấm **Save**.

## 2. Đọc frame (Bước 2)

Ảnh: `t1-s01f16-3d.png`, `t1-s01f16-top.png`, `t1-s01f59-3d.png`, `t1-s01f59-top.png`.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f16 | Phía trước ở bên trái ảnh; +x hướng xuống, +y hướng sang phải | Tương đối phẳng; mặt đường khoảng 1,7 m dưới LiDAR | Khoảng 47 m (cuboid xa nhất trong bảng A) | Xe con, xe buýt | Có xe con phía sau và hai bên; xe buýt ở phía trước | Một số cụm điểm thưa ở rìa ngoài, chưa đủ cơ sở phân loại |
| s01f59 | Phía trước ở bên trái ảnh; +x hướng xuống, +y hướng sang phải | Tương đối phẳng; mặt đường khoảng 1,7 m dưới LiDAR | Khoảng 22 m (cuboid xa nhất trong bảng A) | Xe con, xe hai bánh có người (Cyclist) | Xe con ở nhiều phía; một Cyclist ở phía sau, bên phải | Các điểm thưa ngoài rìa, chưa đủ cơ sở phân loại |

Trong hai frame, ảnh camera cho biết màu sắc/đặc điểm bề mặt, chi tiết nhận diện và dấu hiệu giúp xác định đầu xe hoặc người trên xe. LiDAR cho biết khoảng cách và hình học 3D theo mét, đồng thời thấy vật ở bên/sau ngoài vùng nhìn của camera đang mở. Đây là khác biệt chung giữa hai loại cảm biến; cần đối chiếu trực tiếp camera nếu muốn ghi một ví dụ cụ thể.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f22 | Phía trước bên trái; +x xuống, +y sang phải | Chưa có điều lạ |
| s02f46 | Phía trước bên trái; +x xuống, +y sang phải | Chưa có điều lạ |
| s03f06 | Phía trước bên trái; +x xuống, +y sang phải | Chưa có điều lạ |
| s03f57 | Phía trước bên trái; +x xuống, +y sang phải | Chưa có điều lạ |
| s04f09 | Phía trước bên trái; +x xuống, +y sang phải | Chưa có điều lạ |
| s04f42 | Phía trước bên trái; +x xuống, +y sang phải | Chưa có điều lạ |
| s05f07 | Phía trước bên trái; +x xuống, +y sang phải | Chưa có điều lạ |
| s05f38 | Phía trước bên trái; +x xuống, +y sang phải | Chưa có điều lạ |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f59, 3 | Car | Chưa xác nhận được bằng ảnh camera | Selfcheck: gap 0,11 m; trong ngưỡng 0,3 m | Ảnh có Top/Side/Front; việc đối chiếu camera cần xác nhận | CSV: 1,984 × 4,013 × 1,529 m; dài/rộng có dấu hiệu bị đảo |
| (b) xe buýt hoặc xe tải | s04f09, 12 | Bus | Chưa xác nhận được bằng ảnh camera | Selfcheck: gap 0,15 m; trong ngưỡng 0,3 m | Ảnh có Top/Side/Front; việc đối chiếu camera cần xác nhận | CSV: 2,950 × 11,698 × 3,235 m; dài/rộng có dấu hiệu bị đảo |
| (c) người đi bộ hoặc xe hai bánh | s02f22, 8 | Cyclist | Chưa xác nhận được bằng ảnh camera | Selfcheck: gap 0,21 m; trong ngưỡng 0,3 m | Ảnh có Top/Side/Front; việc đối chiếu camera cần xác nhận | CSV: 1,000 × 1,862 × 1,497 m; dài/rộng có dấu hiệu bị đảo |

Vòng hiệu chuẩn: không có.

## 4. Bốn hiện tượng chất lượng (Bước 4)

Các ảnh dưới đây đều thuộc task cá nhân. Nguyên nhân của phản xạ yếu/điểm bất thường chỉ được nêu như giả thuyết; đối chiếu ảnh camera giúp kiểm tra vật thể nhưng không tự chứng minh nguyên nhân vật lý.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | `s03f06`; [t4-1-bong-che.png](images/t4-1-bong-che.png); task cá nhân | Các xe phía trước chắn tia LiDAR là khả năng gây vùng bóng | Có khoảng thưa/thiếu điểm phía sau các xe, trong khi các bề mặt xung quanh vẫn có điểm | Vùng khuất không đồng nghĩa đường trống; chỉ vẽ phần vật suy ra được và bật `occluded` |
| 2. Thưa ở xa | `s01f16`; [t4-2-thua-o-xa.png](images/t4-2-thua-o-xa.png); task cá nhân | Khoảng cách xa làm số phản hồi giảm | Hộp xe xa có ít điểm; bảng tự kiểm ghi `s01f16#8` ở 47,4 m với 67 điểm | Chỉ vẽ khi đủ bằng chứng về vật và khoảng 15–20% thể tích; nếu không, bỏ qua và ghi `LOW_VISIBILITY` |
| 3. Phản xạ thấp / ít điểm bất thường | `s01f59`; [t4-3-phan-xa-thap.png](images/t4-3-phan-xa-thap.png); task cá nhân | Bề mặt phản xạ yếu hoặc góc quét có thể làm số điểm giảm; chưa thể kết luận nguyên nhân | Trong hộp xe được chọn có ít điểm hơn các xe khác; ảnh camera cùng frame cho thấy xe trong dòng giao thông | Đối chiếu camera và vật cùng tầm; không suy class/kích thước từ cường độ hoặc nới hộp theo vùng phản hồi yếu |
| 4. Điểm lạ | `s05f38`; [t4-4-diem-la.png](images/t4-4-diem-la.png); task cá nhân | Có thể là xe đỗ bị bụi cây che một phần, không nhất thiết là nhiễu | Cụm điểm tách biệt, thưa; ảnh camera cùng frame có một xe đỗ khuất sau bụi cây nên cụm điểm có thể thuộc vật thật | Không gán điểm rời rạc thành vật nếu thiếu bằng chứng; ở đây cần đối chiếu camera và chỉ gán xe nếu đủ điểm/biên dạng để xác định |

## 5. Số từ cuboid của tôi (Bước 5)

Các số dưới đây lấy từ bảng A và phiếu tự kiểm hiện có. `r` được tính từ tâm cuboid: `√(x² + y² + z²)`.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r | yaw_deg | Nhận xét |
|---|---:|---:|---:|---:|---:|---:|---|
| s01f59, 3, Car | -0,928 | 1,529 | -1,693 | 6,61 | 6,68 | 178,5 | Selfcheck báo gap 0,11 m |
| s04f09, 12, Bus | -0,780 | 3,235 | -2,397 | 21,26 | 21,27 | 351,8 | Selfcheck báo gap 0,15 m; kiểm lại trực tiếp trong Side/Front vì z đáy khác mốc đường chung |
| s02f22, 8, Cyclist | -0,783 | 1,497 | -1,531 | 9,84 | 9,87 | 0,0 | Selfcheck báo gap 0,21 m |

- Gần nhất: `s01f59`, idx 1, Car — `dist_m` 2,97 m; `r` 3,11 m.
- Xa nhất: `s01f16`, idx 8, Car — `dist_m` 47,40 m; `r` 47,41 m.
- Kích thước: CSV đang ghi Car mẫu (a) là `l=1,984, w=4,013, h=1,529 m`; nếu hai chiều đầu bị đảo thì thành `4,013 × 1,984 × 1,529 m`, phù hợp xe con. Bus mẫu (b) tương tự: CSV ghi `2,950 × 11,698 × 3,235 m`; đổi hai chiều đầu thì `11,698 × 2,950 × 3,235 m`, phù hợp xe buýt thường. Cyclist mẫu (c), sau khi đổi hai chiều đầu, là `1,862 × 1,000 × 1,497 m`; nếu là xe máy có người thì chiều rộng hơi vượt mốc 0,95 m, cần xác nhận loại xe và hộp trên CVAT.
- Hướng yaw: các mẫu đang có `178,5°` (Car) và `351,8°` (Bus), không đủ để kết luận một xe cùng chiều gần `270°` và một xe ngược chiều gần `90°`. Cần kiểm tra vạch hướng với ảnh camera; không suy ra đúng/sai chỉ từ CSV.

Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---:|---:|
| s01f59, 1, Car | 2,97 | 3169 |
| s01f16, 4, Car | 18,34 | 539 |
| s01f16, 3, Car | 20,17 | 357 |
| s01f16, 7, Car | 31,68 | 274 |

Trong các ví dụ này, xe ở 31,68 m xa gấp khoảng 10,7 lần xe ở 2,97 m nhưng chỉ có khoảng 8,6% số điểm trong hộp. Xu hướng chung là xa hơn thì ít điểm hơn; so với đường cong bài giảng, mẫu 30–40 m ở đây có nhiều điểm hơn mức tham chiếu 72, nên cần nhớ số điểm còn phụ thuộc vật và hộp cụ thể.

83/85 cuboid còn cờ tự kiểm. Cờ `length < width` lặp lại trên hầu hết các xe; bảng A cũng cho thấy dài/rộng bị đảo có tính hệ thống. Tôi chưa sửa CSV bằng tay và sẽ báo coach/kiểm tra trong CVAT theo hướng dẫn. Các trường hợp đáy cần xem lại gồm `s01f59` idx 8 (gap +0,52 m), `s03f06` idx 11 (+0,46 m), `s03f57` idx 5 (-0,45 m), `s05f07` idx 10 (-1,15 m); `s04f09` idx 13 còn cờ 136 điểm sát ngoài mép. Cần xác nhận trường hợp nào đã được giữ sau khi xem lại.

Hai cuboid tôi đã sửa sau `preview`/`selfcheck`: Chưa có.

Đổi kiểm với bạn: Chưa có; chưa đối chiếu số liệu với bảng A của bạn bên cạnh.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

Nhãn 3D dùng mét vì mô hình cần vị trí, kích thước và khoảng cách vật trong không gian thật; pixel thay đổi theo độ phân giải, góc nhìn và khoảng cách camera. Vật thưa điểm có thể bị xa, che khuất hoặc phản xạ yếu, nên cần ngưỡng bằng chứng riêng để tránh vừa bỏ sót vật thật vừa đoán nhãn từ vài điểm rời rạc.

- AI: Có — Copilot/AI assistant trong VS Code. Tôi hỏi cách đọc hướng trục và nhờ đối chiếu ảnh, CSV, hướng dẫn với báo cáo; dùng kết quả để rà số liệu và soạn phần mô tả, còn nhận định cần xác nhận vẫn kiểm tra trên CVAT.
- Điều còn chưa chắc: hướng yaw/đầu xe và chiều dài-rộng do CSV có dấu hiệu đảo có hệ thống; các hiện tượng ở mục 4 chưa có ảnh/ghi chép kèm theo. Cần kiểm lại các cuboid tự kiểm cờ và bổ sung bằng chứng trước khi nộp.
