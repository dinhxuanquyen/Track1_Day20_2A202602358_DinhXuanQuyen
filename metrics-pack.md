# Metrics Pack - SmartCheck-in Kiosk

## 00 — Dự án, persona, core job

- **1. Dự án:** SmartCheck-in Kiosk (Hệ thống web kiosk hỗ trợ khách tự check-in trực tiếp tại khách sạn bằng eKYC/nhận diện khuôn mặt).
- **2. Persona:** Khách lưu trú đã có booking và đến khách sạn để nhận phòng.
- **3. Core job:** "Tôi vừa đi đường mệt, tôi muốn hoàn tất thủ tục nhận phòng và xác thực danh tính nhanh nhất có thể mà không phải xếp hàng chờ đợi hay điền giấy tờ thủ công."

## 01 — Core Action Card (+ kết quả tự kiểm 5 tiêu chí)

| Thành phần                | Câu trả lời của bạn                                                                                                                                              |
| :-------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Target user**       | Khách lưu trú (Guest)                                                                                                                                              |
| **Core job**          | "Tôi muốn hoàn tất thủ tục nhận phòng và xác thực danh tính nhanh nhất có thể mà không phải xếp hàng chờ đợi hay điền giấy tờ thủ công." |
| **Core action**       | **Hoàn tất bước chụp khuôn mặt (Live Face capture) để nộp hồ sơ eKYC nhận phòng.**                                                                |
| **Object**            | Màn hình Kiosk / Hồ sơ check-in (Session)                                                                                                                         |
| **Preconditions**     | Đã nhập booking hợp lệ và đã chụp CCCD thành công.                                                                                                         |
| **Completion rule**   | Giao diện React Kiosk ghi nhận hình ảnh Live Face hợp lệ và bắt đầu gửi đi xử lý đối chiếu (verification).                                           |
| **Core value**        | Rút ngắn thời gian chờ đợi ở sảnh, lấy được thông tin nhận phòng ngay lập tức.                                                                       |
| **Evidence of value** | Hệ thống thông báo cấp phòng thành công (Status:`CHECKED_IN` / `ROOM_ASSIGNED`).                                                                          |
| **Candidate event**   | `live_face_captured`                                                                                                                                                |

### Tự kiểm 5 tiêu chí (Đạt ≥ 4/5)

1. Gần core value — hành vi xảy ra là user đã tiến gần rõ rệt tới value chưa? `(Đạt)`
2. Có thể lặp lại — hành vi có xuất hiện lại khi nhu cầu quay lại không? `(Đạt)`
3. Có thể quan sát — bạn biết chính xác khi nào nó hoàn tất không? `(Đạt)`
4. Có ý nghĩa — hành vi tăng có thật sự nghĩa là sản phẩm tốt hơn không? `(Đạt)`
5. Có thể tác động — team có thể cải thiện khả năng nó xảy ra không? `(Đạt)`

## 02 — Action Nature Card + kết luận cadence

| Thành phần               | Câu hỏi / Trả lời                                                                                           |
| :------------------------- | :-------------------------------------------------------------------------------------------------------------- |
| **Actor**            | Khách lưu trú                                                                                                |
| **Intent**           | Muốn nhận được phòng lưu trú nhanh chóng để nghỉ ngơi.                                             |
| **Trigger**          | Chủ động: Khách đến khách sạn vào ngày lưu trú dựa trên lịch đặt phòng (booking) từ trước. |
| **Effort**           | Thấp (Chỉ tốn 1-2 phút thao tác tại Kiosk, quét giấy tờ và khuôn mặt).                              |
| **Value timing**     | Value xuất hiện ngay lập tức (Nhận thông báo số phòng, chìa khóa ngay sau khi đối chiếu PASS).   |
| **State**            | Trạng thái booking chuyển thành`CHECKED_IN`, lưu lại audit log an toàn.                                |
| **Dependency**       | Phụ thuộc vào việc khách có booking hợp lệ và hệ thống Kiosk hoạt động tốt.                      |
| **Repeat condition** | Khi khách hàng có chuyến đi và phát sinh lượt đặt phòng tiếp theo.                                |

### Kết luận cadence

Đối với **hành vi phản ứng theo sự kiện (event-response)**, core action **chụp khuôn mặt để nộp hồ sơ eKYC** thường xuất hiện **mỗi khi có một booking đến ngày check-in** vì **nhu cầu nhận phòng chỉ phát sinh khi khách thực sự có chuyến đi và cần lưu trú**. Do đó, nhịp đo phù hợp là **theo mỗi lượt booking** ở cấp **User**.

## 03 — Metric System (activation / engagement / NSM / leading / counter)

- **Activation metric:**

  - Start event: `session_started` (Khách bắt đầu nhập `booking_reference` tại Kiosk).
  - Activation event: `room_assigned` (Lần đầu tiên hệ thống xác nhận gán phòng tự động thành công cho khách).
  - Time window: Trong vòng **3 phút** kể từ start event (nếu lâu hơn khách sẽ mất kiên nhẫn và bỏ ngang ra lễ tân).
- **Engagement metric:**

  - **Góc đo Depth:** Tỉ lệ khách hàng đi thẳng một mạch qua các bước (Straight-through Processing) mà không cần retry chụp ảnh hay sửa thông tin (Đo lường chất lượng hoàn thành mỗi lượt).
- **North Star Metric:**

  - [Unit of value: Lượt check-in thành công] + [Quality threshold: Tự động hoàn toàn, không cần HITL] + [Frequency: Hàng tháng]
  - = **Số lượt check-in tự động hoàn toàn (Auto-PASS Check-ins) mỗi tháng.** *(Lưu ý: Đo số lượng giá trị tạo ra, không phải đo tỉ lệ).*
- **Leading indicators (Tối đa 3):**

  - 1. **Tỉ lệ hoàn thành Live Face ngay lần chụp đầu tiên:** Vì trải nghiệm mượt mà, không gián đoạn sẽ khiến khách hàng có ấn tượng tốt và tin tưởng dùng tiếp Kiosk cho lần lưu trú sau.
  - 2. **Tỉ lệ OCR giấy tờ đạt confidence > 90%:** Vì OCR chính xác là tiền đề để Face Verification tự động PASS, chứng tỏ khách nhận được giá trị trọn vẹn, dự báo họ sẽ lặp lại hành vi.
- **Counter-metric (Ít nhất 1):**

  - 1. **Tỉ lệ phiên bị chuyển giao sang lễ tân (HITL Escalation Rate):** Nếu NSM tăng nhưng tỉ lệ đẩy ra quầy thủ công cũng tăng, nghĩa là hệ thống đang đẩy rác sang người, làm tăng khối lượng công việc cho lễ tân.
  - 2. **Thời gian hoàn thành trung bình (Average Session Duration):** Kiosk phục vụ đông khách mà mỗi người mất > 5 phút thì sẽ gây tắc nghẽn sảnh, trải nghiệm tệ đi dù check-in thành công.

## 04 — Retention Definition (6 thành phần)

| Thành phần           | Câu trả lời của bạn                                                                                                      |
| :--------------------- | :---------------------------------------------------------------------------------------------------------------------------- |
| **Unit**         | Khách lưu trú cá nhân (Guest / User).                                                                                    |
| **Cohort entry** | Event`room_assigned` (Lần đầu tiên khách nhận phòng thành công qua Kiosk).                                         |
| **Return event** | Core action`live_face_captured` lặp lại (Khách quay lại khách sạn ở chuyến đi sau và tiếp tục thao tác Kiosk). |
| **Window**       | Project-based (Theo từng chuyến đi / lượt booking tiếp theo của khách).                                               |
| **Threshold**    | Xảy ra 1 lần trong mỗi window (mỗi lượt booking chỉ cần check-in 1 lần).                                             |
| **Segment**      | Returning Guests (Khách đã có ít nhất 2 booking tại hệ thống khách sạn).                                           |

## 05 — Product Loop (2 chu kỳ + metric hypothesis)

- **Loại loop chính:** Event-response (Phản ứng theo sự kiện).
- **Vẽ nhanh loop 2 chu kỳ:**
  - **Chu kỳ 1:** Khách đến ngày lưu trú  -> Chụp Live Face hoàn tất eKYC tại Kiosk  -> Nhận phòng ngay lập tức trong 1-2 phút -> Lịch sử hoàn tất eKYC / Lưu thẻ phòng .
  - **Chu kỳ 2:** Khách có chuyến đi khác quay lại khách sạn  -> Ưu tiên thao tác Kiosk vì đã quen -> Nhận phòng nhanh chóng không phải chờ đợi.
- **Metric hypothesis (Bắt buộc):**
  - Nếu loop này hoạt động, metric **North Star Metric (Số lượt check-in tự động hoàn toàn)** sẽ thay đổi theo hướng **tăng trưởng ổn định** trong **từng tháng**, vì **khách hàng đã từng tiết kiệm được thời gian chờ đợi sẽ tin tưởng và tiếp tục chọn dùng Kiosk thay vì ra quầy lễ tân**.

## 06 — Tracking nhanh (4–8 events + 2 acceptance criteria)

### Core Events (4-8 events)

| Tên event                | Ý nghĩa                                                          | Thời điểm ghi nhận                                                     | Metric sử dụng                                                     |
| :------------------------ | :----------------------------------------------------------------- | :------------------------------------------------------------------------- | :------------------------------------------------------------------- |
| `session_started`       | Khách bắt đầu tiến trình check-in.                           | Ngay khi khách nhập`booking_reference` và bấm "Tiếp tục".          | Start event (Activation), Average Session Duration (Counter-metric). |
| `document_captured`     | Đã quét và trích xuất OCR thông tin giấy tờ thành công. | Khi OCR provider trả về dữ liệu văn bản thành công.                | Leading indicator (Tỉ lệ OCR confidence), Engagement (Depth).      |
| `live_face_captured`    | Chụp khuôn mặt thành công (đây là Core Action).            | Khi Kiosk xác nhận ảnh chuẩn và gửi request lên server xác thực.  | Leading indicator, Return event (Retention).                         |
| `room_assigned`         | Hệ thống tự gán phòng thành công (Value event).             | Khi policy trả về PASS và trạng thái cập nhật thành`CHECKED_IN`. | Activation event, Cohort entry, NSM.                                 |
| `manual_desk_escalated` | Bị lỗi hoặc cần xác minh thủ công (HITL).                   | Khi policy trả về FAIL/REVIEW và đẩy session ra màn hình lễ tân.  | Counter-metric (Tỉ lệ chuyển giao lễ tân).                      |

### Acceptance Criteria (Ít nhất 2 tiêu chí)

1. **Chống ghi trùng do kỹ thuật:** Với mỗi `session_id`, hệ thống chỉ bắn event `room_assigned` 1 lần duy nhất khi trạng thái thực sự chuyển sang `CHECKED_IN`. Việc khách hàng tải lại trình duyệt (F5) ở trang báo kết quả không được phép tạo thêm event.
2. **Ghi nhận hành động thực tế, không phải ý định:** Event `live_face_captured` chỉ được ghi nhận khi ứng dụng thực sự chụp và gửi dữ liệu lên server xử lý. Tuyệt đối KHÔNG bắn event này khi user mới chỉ bấm nút "Mở Camera" hay đang loay hoay căn góc mặt.

---

## 07 — Phase 5: Tự soi lỗi & Rationale

**Đối chiếu 7 câu hỏi GATE 5:**

1. **Core action không phải thao tác giao diện/hệ thống?** - Đạt. Core action em chọn là hành vi chụp Live Face nộp eKYC, không phải thao tác mở máy hay nhấn nút.
2. **Activation không phải "đăng nhập"?** - Đạt. Em chọn activation là `room_assigned`.
3. **Frequency không cao hơn nhu cầu thật?** - Đạt. Em không ép người dùng theo nhịp Daily/Weekly mà đo theo đúng nhịp đi du lịch hoặc công tác tự nhiên.
4. **Loop có reason to return ngoài notification?** - Đạt. Lý do khách hàng quay lại sử dụng Kiosk là vì trải nghiệm trước đó nhanh gọn, giúp họ tiết kiệm thời gian xếp hàng.
5. **Retention window khớp cadence?** - Đạt. Cả Cadence và Window em đều đo theo từng lượt chuyến đi (Project-based).
6. **Mọi event đều map về một metric?** - Đạt. 5 Event em liệt kê ở Phase 4 đều được map cụ thể với các metric tương ứng ở Phase 3.
7. **Metric nào cũng có event để tính nó?** - Đạt. Từ NSM đến Counter-metric đều có event cụ thể để tracking.

**Ghi chú (Revision):**
Bài làm của em được xây dựng dựa trên nhu cầu thực tế của khách hàng là muốn nhận phòng nhanh nhất có thể. Các metric em chọn tập trung vào việc đo lường chất lượng của quá trình tự động hóa (Kiosk tự xử lý) thay vì chỉ đếm số lượng người dùng bề nổi. Sau khi tự soi lỗi, bài làm không vi phạm các lỗi kinh điển nên em xin phép giữ nguyên các quyết định và không chỉnh sửa thêm.
