# Báo cáo Day 13 — Lê Ngọc Dũng, 2A202603103

## 1. Kết luận trước

Task **D13-2A202603103** (Task 16/Job 16), CVAT **2.82.0**, Windows, trình duyệt Chrome. Bản Save có **65 cuboid Shape**, **0 Track**: 60 Car, 1 Bus, 3 Cyclist, 1 Truck. Có **63 hộp trong 50 m**, không hộp rỗng; tự kiểm còn **2/65 hộp có cờ**. Số hộp theo thứ tự 10 frame:

| Frame | Hộp | Frame | Hộp |
|---|---:|---|---:|
| s01f07 | 10 | s03f46 | 6 |
| s01f45 | 7 | s04f01 | 8 |
| s02f32 | 5 | s04f40 | 6 |
| s02f53 | 5 | s05f20 | 6 |
| s03f31 | 5 | s05f45 | 7 |

Đủ mức ≥8 hộp frame đầu và ≥5 mỗi frame sau. Số lượng và cờ tự kiểm không chứng minh đạt điểm; phần cần xem lại nhất là đáy, kích thước và hướng ở vật thưa điểm. Chưa có kết quả chấm với nhãn chuẩn.

Cách dùng: kéo chuột trái để xoay, chuột phải để dời, con lăn để phóng; vẽ Cuboid **Shape**, nháy đúp chọn vật và chỉnh **Top/Side/Front**; bật **Cuboid orientation**, dùng `preview` để đối chiếu trên camera.

## 2. Đọc frame (T1)

Trong hai ảnh Top đã nộp, **phía trước −y sang phải, +x lên trên, +y sang trái** (gần đúng theo góc xoay hiện tại). BEV chuẩn của `preview` có −y ở trên, +x bên trái, +y dưới. Mặt đường tham chiếu z≈−1,7 m; chưa đo riêng độ dốc.

| Frame | Loại vật, xa nhất đã gán | Hai bên/phía sau, chỗ chưa chắc | Ảnh trong ZIP |
|---|---|---|---|
| s01f07 | Car; hộp xa nhất 37,69 m | Xe sát bên trái trên cam05/cam06; #5 và #7 còn cờ | `t1-s01f07-3d.jpg`, `t1-s01f07-top.png` |
| s01f45 | Car; hộp xa nhất 22,16 m | Dòng xe phía trước và làn bên trái; có xe phía sau trên cam09; ranh giới vật bị che chưa chắc | `t1-s01f45-3d.jpg`, `t1-s01f45-top.png` |

Camera cho biết màu sơn, chi tiết vỏ xe và đèn/đầu–đuôi để nhận dạng; LiDAR cho tâm theo mét, độ cao so mặt đường và số điểm trả về. Đối chiếu cả hai để tránh dùng hình dáng suy đoán khi cụm điểm thưa.

Tám frame còn lại: `s02f32`, `s02f53`, `s03f31`, `s03f46`, `s04f01`, `s04f40`, `s05f20`, `s05f45` cùng quy ước −y trước, +x trái, +y sau. Phía trước nằm trên ở BEV chuẩn; chưa có nhật ký xác nhận cạnh tương ứng của từng góc nhìn CVAT.

Hai ảnh Top có lớp mũi tên **chú thích bổ sung**; ảnh gốc được giữ trên máy, không tạo lại point cloud/cuboid. Ảnh camera không đưa lên repo public; tên ảnh trong báo cáo đối chiếu với ZIP nộp LMS.

## 3. Ba cuboid mẫu (T2–T3)

| Vật / frame, idx bảng A | Class | l × w × h (m) | Yaw | Đáy (m) | Ảnh trong ZIP |
|---|---|---|---:|---:|---|
| Xe gần — s01f07 #6 | Car | 4.596 × 1.876 × 1.656 | 268.9° | −1.826 | `t2-a-car.jpg` |
| Xe buýt — s03f31 #3 | Bus | 11.900 × 2.520 × 3.288 | 270.0° | −1.687 | `t2-b-bus-truck.jpg` |
| Xe hai bánh — s03f46 #4 | Cyclist | 2.000 × 0.820 × 1.621 | 90.0° | −1.756 | `t2-c-ped-cyclist.jpg` |

Các ảnh có Top/Side/Front; Bus đối chiếu cam08, Cyclist cam07. Ba đáy gần tham chiếu −1,7 m; hướng đúng đầu–đuôi chưa được xác nhận bằng nhãn chuẩn. Bus/Cyclist lấy ở frame thứ năm/sáu do hai frame đầu thiếu loại mẫu, cần coach chấp nhận ngoại lệ. Chưa tham gia vòng hiệu chuẩn; không ghi ba ảnh là đã được coach hướng dẫn. Cụm cây/cột hoặc vật không đủ cơ sở để nhận loại không gán thành vật chắc chắn.

## 4. Bốn hiện tượng chất lượng (T4)

| Hiện tượng / nguồn / tên ảnh | Quan sát → nguyên nhân giả thuyết → hệ quả gán nhãn |
|---|---|
| Bóng che — task chung D13-HC485087, s02f00; `t4-1-bong-che.jpg` | Xe buýt đỏ sát bên trái; vùng sau xe thiếu điểm. Có thể do chặn tia. Vùng trống chưa được quan sát; bật occluded cho vật bị che nếu vẫn suy luận được. |
| Thưa ở xa — task cá nhân, s04f40 #6; `t4-2-thua-o-xa.jpg` | Xe ở 37,53 m có 100 điểm. Khoảng cách, góc và che khuất có thể giảm số tia trúng vật. Chỉ vẽ khi thấy khoảng 15–20% thể tích hoặc đủ cơ sở suy luận; nếu không, bỏ qua và ghi LOW_VISIBILITY; không nới hộp để gom điểm. |
| Phản xạ thấp/ít điểm — task cá nhân, s01f07 #6; `t4-3-phan-xa-thap.jpg` | Xe xanh tối trên cam05, bề mặt/vùng kính có khoảng trống điểm. Kính hoặc vật liệu tối có thể trả ít điểm; chưa đo intensity, không khẳng định nguyên nhân. Kiểm camera và ranh giới hộp, không bù bằng cách đoán hình dạng. |
| Điểm lạ — task chung D13-HC485087, s02f35; `t4-4-diem-la.jpg` | Góc nhìn ngang cho thấy vệt điểm rời dưới đường, ở phải-dưới ảnh. Theo PHENOMENA.md, cụm sau trái khoảng 29 m có z≈−13,5 m, thấp hơn đường khoảng 11 m. Có thể là nhiễu/phản xạ nhiều đường; không gán thành vật. |

Dùng task chung để xem hiện tượng không đồng nghĩa đã tham gia hiệu chuẩn. Các nguyên nhân trên đều là **giả thuyết**.

## 5. Số từ cuboid và tự kiểm (T5)

Nguồn: `submission/D13-2A202603103-bangA.csv` và `submission/D13-2A202603103-tukiem.csv`. Số liệu sinh bằng hàm gốc của lab từ bản chụp chỉ đọc của task đã Save, không sửa tay CSV. Cột r tính từ số x,y,z trong bảng A.

| Cuboid | z (m) | h (m) | z−h/2 (m) | dist_m (m) | r (m) | yaw |
|---|---:|---:|---:|---:|---:|---:|
| s01f07 #6, Car | −0.999 | 1.656 | −1.826 | 3.62 | 3.753 | 268.9° |
| s03f31 #3, Bus | −0.043 | 3.288 | −1.687 | 21.75 | 21.748 | 270.0° |
| s03f46 #4, Cyclist | −0.946 | 1.621 | −1.756 | 7.04 | 7.105 | 90.0° |

Gần nhất: **s01f07 #6**, dist_m=3,62 m, r=3,753 m. Xa nhất: **s05f45 #7**, dist_m=58,94 m, r=58,954 m, ngoài phạm vi 50 m. Xa nhất trong phạm vi: **s01f07 #10**, dist_m=37,69 m, r=37,693 m.

So bảng 4.3: Car mẫu dài 4,596 m và rộng 1,876 m thuộc khoảng sedan (3,8–5,0; 1,70–1,90 m); cao 1,656 m hơi vượt khoảng 1,40–1,60 m, cần xét dáng xe, không ép theo sedan. Cyclist 2,000×0,820×1,621 m thuộc khoảng xe máy có người (1,8–2,3; 0,70–0,95; 1,40–1,80 m). Hướng tham chiếu: s01f45 #1 yaw=270,0° theo −y; s02f32 #1 yaw=90,0° theo +y.

| Car cùng frame s04f40 | Khoảng cách (m) | Điểm trong hộp |
|---|---:|---:|
| #1 | 4.88 | 4622 |
| #2 | 7.64 | 1900 |
| #4 | 26.01 | 205 |
| #6 | 37.53 | 100 |

Xa gấp 7,69 lần thì số điểm còn 2,16% trong hai mẫu đầu/cuối. Xu hướng giảm phù hợp tham khảo slide; góc, kích thước và che khuất cũng ảnh hưởng, không suy ra quan hệ chỉ theo khoảng cách.

**Rework đã xác minh:** s03f46 #6 thu rộng 2,360→2,001 m (136 điểm); s04f01 #7 thu rộng 2,415→1,921 m (681 điểm), hết cờ kích thước. s05f45 #7 nâng z từ −1,581 lên −1,285 m, chênh đáy còn −0,22 m và hết cờ; s01f07 #2 chỉnh đáy còn lệch 0,02 m. Hai ảnh toàn cảnh frame đầu/thứ hai đã chụp lại.

**Còn hai cảnh báo chưa xử lý dứt điểm:**

| Frame, idx | Thông số và cờ |
|---|---|
| s01f07 #5 | 5,235×2,192×2,054 m; tâm (14,954;6,018) m; đáy cao hơn đường tại chỗ 0,79 m |
| s01f07 #7 | 2,934×2,460×2,092 m; tâm (18,273;22,218) m; dài <3,0 m, rộng >2,3 m theo ngưỡng Car |

Hai hộp chưa đổi thông số trong bản Save mới nhất. Chưa có đủ cơ sở để khẳng định cờ là sai; ghi rõ phần chưa chắc, không chỉ sửa để tắt cờ. ID Objects có thể đổi khi nạp task; nhận diện bằng frame, idx bảng A và kích thước. **Chưa kiểm chéo với bạn học**, chưa có MSSV/hai số đối chiếu.

## 6. Ý nghĩa của nhãn, cách dùng AI và phần chưa chắc

Model học vị trí/kích thước 3D theo mét để ước lượng khoảng cách, không phụ thuộc kích thước pixel của từng camera. Quy tắc cho vật thưa điểm giúp model tránh học hình dạng suy đoán hoặc cụm nhiễu thành đối tượng thật.

- **Công cụ:** ChatGPT/Codex.
- **Nội dung hỗ trợ:** môi trường, tạo task, tạo một phần cuboid nháp, xuất số liệu/tự kiểm, hỗ trợ soạn báo cáo và lớp mũi tên chú thích.
- **Cách dùng kết quả:** học viên xác nhận đã rà soát nhãn; metadata có 42 auto, 1 semi-auto, 22 manual. Không mô tả tất cả nhãn là tự vẽ từ đầu.

Chưa xác nhận đúng đầu–đuôi/IoU/recall theo nhãn chuẩn; phản xạ thấp chỉ là giả thuyết. Hiệu chuẩn, kiểm chéo và ngoại lệ frame mẫu đã nêu đúng trạng thái. Đủ số lượng/11 ảnh chưa chứng minh đạt bài; tổng điểm và các ngưỡng trong RUBRIC.md cần kết quả chấm của coach.
