# BT3_QTCSDL
Thông tin sinh viên 
- Tên:Nguyễn Văn An
- Lớp:K59KMT.K01
- MSSV:K235480106002
- Đề tài: Thiết kế và cài đặt CSDL quản lý cầm đồ
# Nhiệm vụ 1: Thiết kế CSDL 
<img width="2560" height="1600" alt="Ảnh chụp màn hình 2026-05-11 091346" src="https://github.com/user-attachments/assets/4bb5adce-6bf0-430d-931d-b6bba47a239f" />
Tạo csdl mới với tên QuanLyCamDo
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/e8735f0f-a6a6-4130-a679-7e8ae753001d" />
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/ead53a47-3fc9-4e3c-889c-44f676736921" />
*Ảnh tạo các bảng cùng các rằng buộc, khóa chính, khóa ngoại

## 1. BẢNG KHACH_HANG (Khách hàng)

| Tên cột | Kiểu dữ liệu | Ràng buộc | Giải thích |
|---------|-------------|-----------|------------|
| MaKH | CHAR(10) | PRIMARY KEY | Mã khách hàng, định danh duy nhất |
| TenKH | NVARCHAR(100) | NOT NULL | Họ tên đầy đủ của khách hàng |
| SDT | VARCHAR(15) | NOT NULL | Số điện thoại liên hệ |
| DiaChi | NVARCHAR(200) | NULL | Địa chỉ cư trú |
| CCCD | CHAR(12) | UNIQUE, NOT NULL | Căn cước công dân, mỗi người một số |
| NgayDangKy |	DATE	|DEFAULT GETDATE()|	Ngày khách đăng ký 	|

Giải thích

Mỗi khách hàng có một mã duy nhất để hệ thống quản lý

CCCD là duy nhất, không thể có 2 khách hàng trùng CCCD


## 2. BẢNG HOP_DONG (Hợp đồng vay)

| Tên cột | Kiểu dữ liệu | Ràng buộc | Giải thích |
|---------|-------------|-----------|------------|
| MaHD | CHAR(10) | PRIMARY KEY | Mã hợp đồng, định danh duy nhất |
| MaKH | CHAR(10) | FOREIGN KEY | Mã khách hàng (liên kết bảng KHACH_HANG) |
| SoTienGoc | DECIMAL(18,2) | NOT NULL | Số tiền vay ban đầu |
| NgayVay | DATE | DEFAULT GETDATE() | Ngày giải ngân, bắt đầu tính lãi |
| Deadline1 | DATE | NOT NULL | Hạn cuối tính lãi đơn |
| Deadline2 | DATE | NOT NULL | Hạn cuối trước khi thanh lý |
| TrangThai | CHAR(20) | DEFAULT 'Đang vay', CHECK | Trạng thái hiện tại của hợp đồng |

CHECK (TrangThai)	Trạng thái chỉ được là: Đang vay, Quá hạn, Đã thanh toán, Đã thanh lý, Đang trả góp

CHECK (Deadline2 > Deadline1)	Ngày thanh lý phải sau ngày hết hạn lãi đơn

CHECK (NgayVay <= Deadline1)	Ngày vay không được sau ngày hết hạn lãi đơn


Trạng thái	Ý nghĩa	Điều kiện

Đang vay	Hợp đồng còn hiệu lực, khách chưa quá hạn	NgayHienTai <= Deadline1

Quá hạn	Khách đã quá Deadline1 nhưng chưa thanh lý	Deadline1 < NgayHienTai < Deadline2

Đang trả góp	Khách đã trả một phần nhưng chưa hết nợ	CONNO > 0

Đã thanh toán	Khách đã trả hết nợ gốc và lãi	CONNO = 0

Đã thanh lý	Tài sản đã bị bán để thu hồi nợ	NgayHienTai > Deadline2

## 3. BẢNG TAI_SAN (Tài sản cầm cố)

| Tên cột | Kiểu dữ liệu | Ràng buộc | Giải thích |
|---------|-------------|-----------|------------|
| MaTS | CHAR(10) | PRIMARY KEY | Mã tài sản, định danh duy nhất |
| TenTS | NVARCHAR(200) | NOT NULL | Tên tài sản |
| LoaiTS | NVARCHAR(50) | NULL | Phân loại tài sản |
| GiaTriDinhGia | DECIMAL(18,2) | NOT NULL, CHECK > 0 | Giá trị thẩm định của tài sản |
| MaHD | CHAR(10) | FOREIGN KEY | Mã hợp đồng (liên kết bảng HOP_DONG) |
| TrangThai | CHAR(20) | DEFAULT 'Đang cầm', CHECK | Trạng thái hiện tại của tài sản |

CHECK (TrangThai)	Trạng thái chỉ được là: Đang cầm, Sẵn sàng thanh lý, Đã bán thanh lý, Đã trả

CHECK (GiaTriDinhGia > 0)	Giá trị định giá phải lớn hơn 0

Trạng thái	Ý nghĩa	Khi nào xảy ra

Đang cầm	Tài sản đang được cầm cố tại cửa hàng	Khi mới tạo hợp đồng

Sẵn sàng thanh lý	Tài sản đã quá Deadline2, chuẩn bị bán	Khi NgayHienTai > Deadline2

Đã bán thanh lý	Tài sản đã được bán để thu hồi nợ	Sau khi thanh lý thành công

Đã trả	Tài sản đã trả lại cho khách	Khi khách trả hết nợ

## 4. BẢNG LOG_GIAO_DICH (Lịch sử giao dịch)

| Tên cột | Kiểu dữ liệu | Ràng buộc | Giải thích |
|---------|-------------|-----------|------------|
| MaLog | CHAR(10) | PRIMARY KEY | Mã giao dịch, định danh duy nhất |
| MaHD | CHAR(10) | FOREIGN KEY | Mã hợp đồng (liên kết bảng HOP_DONG) |
| NgayTra | DATE | NOT NULL | Ngày khách hàng thanh toán |
| SoTienTra | DECIMAL(18,2) | NOT NULL, CHECK > 0 | Số tiền trả trong lần này |
| ConNo | DECIMAL(18,2) | NOT NULL, CHECK >= 0 | Số dư nợ còn lại sau khi trả |
| PhuongThucThanhToan | VARCHAR(20) | DEFAULT 'Tiền mặt' | Hình thức thanh toán |

CHECK (SoTienTra > 0)	Số tiền trả phải lớn hơn 0

CHECK (ConNo >= 0)	Số dư nợ không được âm

Vai trò của bảng LOG:

Ghi lại tất cả các lần trả tiền của khách hàng

Tránh mất dấu vết dòng tiền (Audit Trail)

Tính toán chính xác số tiền đã trả và còn nợ
### Sơ đồ  ERD
<img width="2560" height="1600" alt="Ảnh chụp màn hình 2026-05-11 103619" src="https://github.com/user-attachments/assets/3fa6e80a-f0a4-408c-bcf4-6fe3d2f22004" />
*Ảnh này là sơ đồ ERD

 Bảng KHACH_HANG (Khách hàng)
Vai trò: Lưu trữ thông tin cá nhân của người vay tiền.

 Bảng HOP_DONG (Hợp đồng vay)
Vai trò: Lưu thông tin từng hợp đồng vay tiền của khách hàng.

 Bảng TAI_SAN (Tài sản cầm cố)
Vai trò: Lưu danh sách tài sản mà khách hàng thế chấp.

 Bảng LOG_GIAO_DICH (Lịch sử giao dịch)
Vai trò: Ghi lại lịch sử trả nợ (Audit Log), tránh mất dấu vết dòng tiền.

🔗 Quan hệ 1: KHACH_HANG - HOP_DONG

KHACH_HANG (1) ──────────► (N) HOP_DONG
    MaKH                      MaKH (FK)

Loại quan hệ	1 - N (Một - Nhiều)

Chiều	Một khách hàng có thể có nhiều hợp đồng

Ràng buộc	Không thể tạo hợp đồng cho khách hàng chưa tồn tại

🔗 Quan hệ 2: HOP_DONG - TAI_SAN

HOP_DONG (1) ─────────────► (N) TAI_SAN
    MaHD                       MaHD (FK)

Loại quan hệ	1 - N (Một - Nhiều)

Chiều	Một hợp đồng có thể cầm cố nhiều tài sả

Ràng buộc	Xóa hợp đồng thì tự động xóa tài sản (ON DELETE CASCADE)

🔗 Quan hệ 3: HOP_DONG - LOG_GIAO_DICH

HOP_DONG (1) ─────────────► (N) LOG_GIAO_DICH
    MaHD                       MaHD (FK)

Loại quan hệ	1 - N (Một - Nhiều)

Chiều	Một hợp đồng có thể có nhiều lần trả tiền

Ràng buộc	Không thể ghi log cho hợp đồng không tồn tại
# Nhiệm vụ 2: Cài đặt SQL
## Event 1: Đăng ký hợp đồng mới (Vay tiền)
<img width="2560" height="1600" alt="Ảnh chụp màn hình 2026-05-11 105516" src="https://github.com/user-attachments/assets/23510b37-e0c7-41cb-a3eb-94c3e8bb63a0" />
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/c3a7b1ce-7107-434f-8f69-617e801133c3" />
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/3e601776-7300-4be8-a149-3132bb39c46c" />

*Ảnh này tạo 1  Store Procedure để  tiếp nhận hợp đồng 

Các việc nó thực hiện chính

	|Lưu thông tin khách hàng	| Tự động thêm mới hoặc cập nhật|
  
	|Lưu danh sách tài sản|	 Hỗ trợ nhiều tài sản, kèm giá trị định giá|
  
	|Lưu số tiền vay gốc	| Lưu vào bảng HOP_DONG|
  
	|Thiết lập Deadline1	| Lưu vào bảng HOP_DONG|

  
	|Thiết lập Deadline2|	 Lưu vào bảng HOP_DONG|
  
	|Tự sinh mã|	 Tự sinh mã KH, HD, TS, LOG|
  
	|Ghi Audit Log|	Ghi log khởi tạo|
  
	|Transaction|	Đảm bảo toàn vẹn dữ liệu|
## Event 2: Tính toán công nợ thời gian thực
### Viết một Function fn_CalcMoneyTransaction(TransactionID, TargetDate) để tính số tiền phải trả của TransactionID này cho đến ngày TargetDate
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/dac65f58-d9e0-4ca0-97f0-4ae997495540" />

Function này trả về số dư nợ còn lại sau một giao dịch cụ thể, tính đến ngày TargetDate.

-Công việc fn này làm

1	Nhận vào mã giao dịch (TransactionID)	VD: 'LOG0000001'

2	Nhận vào ngày cần tính (TargetDate)	VD: '2026-06-15'

3	Tìm trong bảng LOG_GIAO_DICH	Tìm dòng có mã log trùng

4	Lấy cột ConNo (số dư nợ sau giao dịch)	VD: 8,000,000đ

5	Trả về số tiền phải trả của giao dịch đó	Tính đến ngày TargetDate

### Viết một Function fn_CalcMoneyContract(ContractID, TargetDate) để tính tổng số tiền khách(ContractID) phải trả (Gốc + Lãi đơn + Lãi kép) tính đến ngày TargetDate.
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/64763adc-caf4-4b51-9a26-38e6a18e9092" />
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/3dc39a32-e9af-4ce8-ab50-47d80a48ca6f" />
Tính tổng nợ (gốc + lãi) của hợp đồng

-Công việc fn này làm

1	Nhận vào mã hợp đồng (ContractID)	VD: 'HD00000001'

2	Nhận vào ngày cần tính (TargetDate)	VD: '2026-06-15'

3	Lấy thông tin hợp đồng từ bảng HOP_DONG	Gốc, Ngày vay, Deadline1

4	Tính lãi đơn (nếu chưa quá Deadline1)	Gốc × 0.005 × số ngày

5	Tính lãi kép (nếu đã quá Deadline1)	Gốc_mới × (1.005)^số ngày quá hạn

6	Tính tổng tiền phải trả	Gốc + Lãi đơn + Lãi kép

7	Trừ đi số tiền đã trả	Lấy SUM(SoTienTra) từ bảng LOG

8	Trả về số tiền khách còn nợ	Tính đến ngày TargetDate 

## Event 3: Xử lý trả nợ và hoàn trả tài sản 
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/4dac006c-2198-4001-9765-2be21334b01e" />
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/fb2a217c-4e26-484c-9ccd-3f0a8a1b2d59" />
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/cff556b8-fef4-44c8-9f81-80ef70b61163" />
*Ảnh này tạo Store Procedure để Xử lý trả nợ và hoàn trả tài sản

- Các công việc mà  PROCEDURE thực hiện 
1 Lấy thông tin hợp đồng	Lấy TrangThai và Deadline2 để kiểm tra
  
2	Kiểm tra tài sản thanh lý	 Nếu đã bán → báo lỗi, không thu tiền

3	Tính tổng nợ hiện tại	 Dùng fn_CalcMoneyContract

4	Trừ số tiền khách trả	 Tính nợ còn lại

4	Nếu trả hết → cập nhật "Đã thanh toán"	 Update HOP_DONG + TAI_SAN

6	Nếu chưa hết → cập nhật "Đang trả góp"	 Update HOP_DONG

7	Ghi nhận LOG	 Insert vào LOG_GIAO_DICH

8	Gợi ý trả tài sản	 Xuất danh sách tài sản đủ giá trị

## Event 4: Truy vấn danh sách nợ xấu (Nợ khó đòi)
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/e31d1aaf-7d40-4bb7-b4c0-a58d499d44e1" />
*Ảnh này tạo  Procedure để Truy vấn danh sách nợ xấu

1	Lấy danh sách hợp đồng	Chỉ lấy hợp đồng đang vay hoặc đang trả góp

2	Lọc khách nợ xấu	Lấy hợp đồng đã quá Deadline1

3	Lấy tên khách hàng	Từ bảng KHACH_HANG
4	Lấy số điện thoại	Từ bảng KHACH_HANG

5	Lấy số tiền vay gốc	Từ bảng HOP_DONG

6	Tính số ngày quá hạn	Lấy ngày hôm nay - Deadline1

## Event 5: Quản lý thanh lý tài sản 
### Viết một Trigger tự động chuyển trạng thái hợp đồng sang "Quá hạn (nợ xấu)" sau khi hợp đồng đang ở trạng thái "Đang vay" mà ngày vượt quá Deadline 1.
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/2e539201-b0b5-482f-969d-3ca7140a5908" />
*Trigger này chạy mỗi khi có INSERT hoặc UPDATE vào bảng HOP_DONG. Nếu hợp đồng đang "Đang vay" mà ngày hiện tại đã qua Deadline1 → tự động chuyển thành "Quá hạn".

### Viết một Trigger tự động chuyển trạng thái tài sản sang "Sẵn sàng thanh lý" sau khi hợp đồng đang ở trạng thái "Quá hạn (nợ xấu)" mà ngày vượt quá Deadline 2.
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/12595579-c896-4f71-be5d-7862f85eb4e6" />
*Trigger này chạy khi UPDATE bảng HOP_DONG. Nếu hợp đồng "Quá hạn" và đã qua Deadline2 → chuyển tài sản sang "Sẵn sàng thanh lý".

### Viết một Trigger tự động chuyển trạng thái tài sản thành “Đã bán thanh lý” sau khi trạng thái của hợp đồng chuyển sang "Đã thanh lý".
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/fd40e7dd-e4ab-45d6-8d80-f56c6a5ad605" />
*Trigger này chạy khi cột TrangThai của HOP_DONG được UPDATE. Nếu hợp đồng chuyển thành "Đã thanh lý" → tài sản tự động thành "Đã bán thanh lý".

#### BẢNG TRẠNG THÁI TÀI SẢN

| Trạng thái | Ý nghĩa | Khi nào xảy ra |
|------------|---------|----------------|
| Đang cầm | Tài sản đang được cầm cố tại cửa hàng | Khi mới tạo hợp đồng, khách chưa trả nợ |
| Sẵn sàng thanh lý | Tài sản đã quá hạn, chuẩn bị bán ra thị trường | Hợp đồng ở trạng thái "Quá hạn" và ngày hiện tại > Deadline2 |
| Đã bán thanh lý | Tài sản đã được bán để thu hồi nợ | Hợp đồng chuyển sang trạng thái "Đã thanh lý" |
| Đã trả | Tài sản đã được trả lại cho khách hàng | Khách hàng trả hết nợ (gốc + lãi) |

## Sự kiện bổ sung:Gia hạn hợp đồng
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/bdfb97f1-0690-48aa-9330-963774eb1cd7" />

#### Công việc sp thực hiện

| STT | Công việc | Giải thích |
|-----|-----------|------------|
| 1 | Nhận đầu vào | @ContractID, @NewDeadline1, @NewDeadline2, @NguoiThu |
| 2 | Kiểm tra hợp đồng | Xem hợp đồng có tồn tại không |
| 3 | Tính tổng lãi phải trả | Gọi fn_CalcMoneyContract để tính gốc + lãi đến hiện tại |
| 4 | Tạo mã log mới | Sinh mã MaLog tự động (LOGxxxxxxx) |
| 5 | Ghi nhận giao dịch | Insert vào LOG_GIAO_DICH: số tiền lãi đã trả, ConNo = 0 |
| 6 | Cập nhật Deadline mới | Gán Deadline1 và Deadline2 mới cho hợp đồng |
| 7 | Đưa về trạng thái "Đang vay" | Reset trạng thái hợp đồng về "Đang vay" |
| 8 | Commit transaction | Lưu tất cả thay đổi |
| 9 | Trả về kết quả | Thông báo thành công + số tiền đã trả + Deadline mới |

### Chạy thử lại các tính năng của chương trình
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/8f9444e7-2bf2-470a-acc3-9cddb5a76c0e" />
*Thêm vào dữ liệu mẫu một vài khách hàng thành công
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/9198f530-2353-4815-99e4-b9028dfaa2e7" />
*Thêm vào dữ liệu mẫu một vài hợp đồng thành công
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/4649f8af-877a-492c-bbc3-fc462bae8bea" />
*Thêm vào dữ liệu mẫu một vài tài sản thành công
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/77eee9c2-803a-40d4-89b6-f8583000a128" />
*Thêm vào dữ liệu mẫu một vài log giao dịch thành công
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/1a5fefc1-2405-4fe0-82c4-31ce381e311f" />
*Kiểm tra fn_CalcMoneyTransaction của event 2 thành công
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/df6fa336-b0d2-4cc1-a868-dee34efd8b30" />
*Kiểm tra fn_CalcMoneyContract của event 2 thành công
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/ae73be60-1655-4e94-861c-b52a82468e03" />
*KIỂM TRA XỬ LÝ TRẢ NỢ (EVENT 3) thành công
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/e59b130b-4d59-4c01-abd0-b38f87858674" />
*KIỂM TRA DANH SÁCH NỢ XẤU (EVENT 4) thành công
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/c0f7fbab-fd6c-44e0-9c6b-b34e506cf8cf" />
*KIỂM TRA TRIGGER TỰ ĐỘNG (EVENT 5) thành công
<img width="2560" height="1600" alt="image" src="https://github.com/user-attachments/assets/e54157e2-cbf6-4b67-9f98-c9aa57d88cac" />
*Kiêm tra tính năng gia hạn hợp đồng thành công
