# Báo Cáo Thống Kê Công Nợ Tích Lũy (Debt Accumulation Report)

## Mô Tả Tổng Quan - Phiên Bản Mới

Báo cáo thống kê công nợ tích lũy là tính năng cho phép kế toán xem **tổng số tiền nợ chưa thanh toán** tại **thời điểm cuối tháng** được chọn. Khác với cách hiểu cũ (tính chi phí phát sinh TRONG tháng), cách hiểu mới này **nhìn ngược về trước** từ cuối tháng để thống kê tổng công nợ tích lũy đến thời điểm đó.

### Đặc Điểm Thanh Toán Thực Tế
- **Chu kỳ thanh toán phổ biến:** Theo đợt 3 tháng (không phải hàng tháng)
- **Ngày bắt đầu đợt:** Có thể là ngày bất kỳ trong tháng (không nhất thiết ngày 1)
- **Ví dụ đợt bắt đầu ngày 15:**
  - Đợt 1: 15/T1 → 14/T4 (3 tháng)
  - Đợt 2: 15/T4 → 14/T7 (3 tháng)
  - Đợt 3: 15/T7 → 14/T10 (3 tháng)
  - Đợt 4: 15/T10 → 14/T1+1 (3 tháng)
- **Tính nợ:** Nếu đợt 3 tháng chưa thanh toán → nợ cả đợt đó
- **Thời điểm báo cáo:** Cuối tháng bất kỳ, xem tổng nợ tích lũy đến thời điểm đó

## Sự Khác Biệt Cơ Bản

### Cách Hiểu Cũ (Period of Time)
- Tháng chọn = **Khung thời gian**
- Tính chi phí **phát sinh TRONG** tháng T
- Logic: "Tháng này tôi phải trả bao nhiêu?"

### Cách Hiểu Mới (Point of Time) - ĐÃ CHỌN
- Tháng chọn = **Thời điểm cuối tháng**
- Tính tổng nợ **tích lũy ĐẾN** cuối tháng T
- Logic: "Đến cuối tháng này, tôi còn nợ tổng cộng bao nhiêu?"

## Yêu Cầu Chính Theo Cách Hiểu Mới

### 1. Thống Kê Công Nợ Tích Lũy Theo Đợt
- Tính tổng số tiền chưa thanh toán của tất cả PLHD_MatBang đến cuối tháng được chọn
- **Đặc biệt:** Thanh toán theo đợt 3 tháng, nên nợ thường tích lũy theo từng đợt
- Bao gồm cả nợ cũ từ các đợt trước và nợ phát sinh trong đợt hiện tại
- Không phân biệt nợ phát sinh khi nào, chỉ quan tâm tổng nợ tại thời điểm cuối tháng

### 2. Logic Ưu Tiên Dữ Liệu (Giữ Nguyên)
- **Ưu tiên 1:** Kiểm tra số liệu thực tế trong ChiPhiTMB
- **Ưu tiên 2:** Tính toán lý thuyết từ PLHD_MatBang nếu không có số liệu thực tế

### 3. Công Thức Tính Toán Theo Đợt 3 Tháng
- Tính tổng số tiền từ đợt thanh toán cuối cùng đến cuối tháng được chọn
- Sử dụng F_CalculateNgayThanhToanKyMoi để xác định các đợt thanh toán 3 tháng
- Cộng dồn tất cả các đợt chưa thanh toán đến thời điểm cuối tháng
- **Ví dụ:** Nếu đợt T4-T6 chưa thanh toán và đang ở cuối T5 → nợ = tiền thuê 3 tháng (T4+T5+T6)

## Đồ Thị Logic Flow Mới

```mermaid
graph TD
    A[Chọn Tháng T - Thời Điểm Cuối Tháng] --> B{Kiểm tra ChiPhiTMB}
    
    B -->|Có số liệu thực tế| C[Lấy số liệu thực tế từ ChiPhiTMB]
    B -->|Không có số liệu| D[Tính toán từ PLHD_MatBang]
    
    D --> E[Xác định tất cả PLHD còn hoạt động đến cuối tháng T]
    
    E --> F[Tính tổng nợ tích lũy cho từng PLHD]
    
    F --> G{Trạng thái PLHD tại cuối tháng T}
    
    G -->|Còn hoạt động| H[Tính nợ từ lần TT cuối đến cuối tháng T]
    G -->|Đã hết hạn| I[Tính nợ từ lần TT cuối đến ngày hết hạn]
    G -->|Đã hủy| J[Tính nợ từ lần TT cuối đến ngày hủy]
    G -->|Quá hạn| K[Tính toàn bộ nợ quá hạn]
    
    H --> L[Cộng dồn tất cả công nợ]
    I --> L
    J --> L
    K --> L
    
    C --> M[Kết hợp số liệu thực tế + lý thuyết]
    L --> M
    
    M --> N[Báo cáo tổng công nợ tại cuối tháng T<br/>- Phân loại theo trạng thái PLHD<br/>- Hiển thị tổng nợ tích lũy]

    style A fill:#e1f5fe
    style C fill:#c8e6c9
    style L fill:#fff3e0
    style N fill:#f3e5f5
```

## Các Trường Hợp Chi Tiết - Công Nợ Tích Lũy

### Case 1: PLHD Hoạt động bình thường - Có nợ tích lũy theo đợt

```mermaid
timeline
    title Case 1 - PLHD hoạt động, có nợ tích lũy theo đợt 3 tháng
    
    section Đợt 1 (15/T1-14/T4)
        Đã thanh toán : Đợt đã thanh toán đầy đủ
    
    section Đợt 2 (15/T4-14/T7)
        Đã thanh toán : Đợt đã thanh toán đầy đủ
    
    section Đợt 3 (15/T7-14/T10)
        Chưa thanh toán : Đợt chưa thanh toán
        Cuối T8 : TỔNG NỢ = Tiền thuê 3 tháng (15/T7-14/T10)
    
    section Đợt 4 (15/T10-14/T1+1)
        Chưa đến hạn : Đợt chưa bắt đầu
```

**Điều kiện:**
- PLHD_MatBang còn hoạt động đến cuối tháng T (ví dụ T8)
- Có đợt thanh toán 3 tháng chưa thực hiện (15/T7-14/T10)
- Thời hạn (thoihanplhd) > cuối tháng T
- **Ngày bắt đầu đợt:** 15 hàng tháng (có thể là ngày bất kỳ)

**Tính toán:**
- Tổng nợ = Tất cả các đợt 3 tháng chưa thanh toán đến cuối tháng T
- Công thức: `SUM(Giá_đợt_3_tháng)` cho các đợt chưa thanh toán
- **Ví dụ cụ thể:** Đang ở cuối T8, đợt 15/T7-14/T10 chưa thanh toán → Nợ = Giá thuê × 3 tháng

---

### Case 2: PLHD Hết hạn trong tháng T - Nợ đến ngày hết hạn

```mermaid
timeline
    title Case 2 - PLHD hết hạn trong tháng T
    
    section Đợt 1 (T1-T3)
        Đã thanh toán : Đợt T1-T3 đã thanh toán đầy đủ
    
    section Đợt 2 (T4-T6)
        Đã thanh toán : Đợt T4-T6 đã thanh toán đầy đủ
    
    section Đợt 3 (T7-T9)
        Chưa thanh toán : Đợt T7-T9 chưa thanh toán
        Ngày 18/T8 : HẾT HẠN (giữa đợt)
        Cuối T8 : TỔNG NỢ = Tiền thuê từ T7 đến 18/T8
    
    section Đợt 4 (T10-T12)
        Không áp dụng : Hợp đồng đã hết hạn
```

**Điều kiện:**
- PLHD_MatBang hết hạn trong tháng T (ví dụ 18/T8)
- Thời hạn (thoihanplhd) nằm trong tháng T, có thể giữa một đợt 3 tháng

**Tính toán:**
- Tổng nợ = Nợ tích lũy đến ngày hết hạn, tính theo tỷ lệ trong đợt 3 tháng
- Công thức: `Giá_đợt_3_tháng × (Số_ngày_từ_đầu_đợt_đến_hết_hạn / Số_ngày_cả_đợt)`
- **Ví dụ cụ thể:** Đợt T7-T9 chưa thanh toán, hết hạn 18/T8 → Nợ = Giá thuê × (T7 + T8 đến ngày 18) / 3 tháng

---

### Case 3: PLHD Bị hủy trong tháng T - Nợ đến ngày hủy

```mermaid
timeline
    title Case 3 - PLHD bị hủy trong tháng T
    
    section Đợt 1 (T1-T3)
        Đã thanh toán : Đợt T1-T3 đã thanh toán đầy đủ
    
    section Đợt 2 (T4-T6)
        Đã thanh toán : Đợt T4-T6 đã thanh toán đầy đủ
    
    section Đợt 3 (T7-T9)
        Chưa thanh toán : Đợt T7-T9 chưa thanh toán
        Ngày 15/T8 : BỊ HỦY (giữa đợt)
        Cuối T8 : TỔNG NỢ = Tiền thuê từ T7 đến 15/T8
    
    section Đợt 4 (T10-T12)
        Không áp dụng : Hợp đồng đã hủy
```

**Điều kiện:**
- PLHD_MatBang bị hủy trong tháng T (ví dụ 15/T8)
- Có ngày hủy (ngayhuy_plhd) nằm trong tháng T, có thể giữa một đợt 3 tháng

**Tính toán:**
- Tổng nợ = Nợ tích lũy đến ngày hủy, tính theo tỷ lệ trong đợt 3 tháng
- Công thức: `Giá_đợt_3_tháng × (Số_ngày_từ_đầu_đợt_đến_ngày_hủy / Số_ngày_cả_đợt)`
- **Ví dụ cụ thể:** Đợt T7-T9 chưa thanh toán, hủy 15/T8 → Nợ = Giá thuê × (T7 + T8 đến ngày 15) / 3 tháng

---

### Case 4: PLHD Quá hạn - Nợ toàn bộ các đợt

```mermaid
timeline
    title Case 4 - PLHD quá hạn chưa thanh toán
    
    section Đợt 1 (T1-T3)
        Đã thanh toán : Đợt T1-T3 đã thanh toán đầy đủ
    
    section Đợt 2 (T4-T6)
        Chưa thanh toán : Đợt T4-T6 chưa thanh toán (QUÁ HẠN)
    
    section Đợt 3 (T7-T9)
        Chưa thanh toán : Đợt T7-T9 chưa thanh toán (QUÁ HẠN)
        Cuối T8 : TỔNG NỢ = 2 đợt × 3 tháng = 6 tháng tiền thuê
    
    section Đợt 4 (T10-T12)
        Chưa đến hạn : Đợt chưa bắt đầu
```

**Điều kiện:**
- PLHD đã quá thời hạn thanh toán nhiều đợt
- Chưa thanh toán (chưa chuyển hoặc đã chuyển nhưng chưa duyệt)

**Tính toán:**
- Tổng nợ = Toàn bộ các đợt 3 tháng chưa thanh toán
- Ưu tiên cao trong báo cáo (màu đỏ)
- Công thức: `SUM(Giá_đợt_3_tháng × Số_đợt_chưa_thanh_toán)`
- **Ví dụ cụ thể:** 2 đợt quá hạn (T4-T6, T7-T9) → Nợ = Giá thuê × 6 tháng

---

## Tổng Hợp Logic Tính Toán Mới

### Nguyên Tắc Cơ Bản
1. **Thời điểm tham chiếu:** Cuối tháng được chọn
2. **Phương pháp:** Nhìn ngược về trước, tính tổng nợ tích lũy
3. **Phạm vi:** Tất cả PLHD có liên quan đến thời điểm cuối tháng
4. **Kết quả:** Tổng công nợ chưa thanh toán tại thời điểm cuối tháng

### Ma Trận Tính Toán Theo Đợt 3 Tháng

| Trạng thái PLHD tại cuối tháng T | Phạm vi tính nợ | Công thức |
|----------------------------------|-----------------|-----------|
| Còn hoạt động | Các đợt 3 tháng chưa thanh toán | SUM(Giá_đợt × Số_đợt_chưa_TT) |
| Hết hạn trong tháng T | Từ đợt cuối → Ngày hết hạn | Giá_đợt × Tỷ_lệ_thời_gian |
| Hủy trong tháng T | Từ đợt cuối → Ngày hủy | Giá_đợt × Tỷ_lệ_thời_gian |
| Quá hạn | Toàn bộ các đợt quá hạn | SUM(Giá_đợt × Số_đợt_quá_hạn) |

### Ví Dụ Thực Tế Chu Kỳ Thanh Toán

**Trường hợp 1: Hợp đồng bắt đầu ngày 1 (truyền thống):**
- **Đợt 1:** 01/T1 → 31/T3 (Quý 1) - Thanh toán vào cuối T3
- **Đợt 2:** 01/T4 → 30/T6 (Quý 2) - Thanh toán vào cuối T6  
- **Đợt 3:** 01/T7 → 30/T9 (Quý 3) - Thanh toán vào cuối T9
- **Đợt 4:** 01/T10 → 31/T12 (Quý 4) - Thanh toán vào cuối T12

**Trường hợp 2: Hợp đồng bắt đầu ngày 15 (thực tế phổ biến):**
- **Đợt 1:** 15/T1 → 14/T4 (3 tháng) - Thanh toán vào 14/T4
- **Đợt 2:** 15/T4 → 14/T7 (3 tháng) - Thanh toán vào 14/T7
- **Đợt 3:** 15/T7 → 14/T10 (3 tháng) - Thanh toán vào 14/T10
- **Đợt 4:** 15/T10 → 14/T1+1 (3 tháng) - Thanh toán vào 14/T1+1

**Tình huống báo cáo tại cuối T8 (trường hợp 2):**
- Đợt 1 (15/T1-14/T4): ✅ Đã thanh toán
- Đợt 2 (15/T4-14/T7): ✅ Đã thanh toán  
- Đợt 3 (15/T7-14/T10): ❌ Chưa thanh toán → **NỢ = Tiền thuê 3 tháng**
- Đợt 4 (15/T10-14/T1+1): ⏳ Chưa đến hạn

**Lưu ý quan trọng cho Implementation:**
- Function `F_CalculateNgayThanhToanKyMoi` phải xử lý chính xác ngày bắt đầu bất kỳ
- Không thể dùng công thức đơn giản theo tháng dương lịch
- Cần tính toán chính xác dựa trên `ngaythanhtoan_plhd` trong PLHD_MatBang

### Công Nghệ Sử Dụng (Giữ Nguyên)

#### Database Functions
- **F_CalculateNgayThanhToanKyMoi:** Tính toán ngày cuối của một đợt thanh toán
- **sp_GetHD_ThanhToan_DaCoChungTu_RBAC:** Stored procedure tham khảo

#### Tables Chính
- **PLHD_MatBang:** Chứa thông tin phụ lục hợp đồng mặt bằng
- **ChiPhiTMB:** Bảng chi phí thuê mặt bằng (số liệu thực tế)
- **vw_ChiPhiTMBMoiNhat:** View lấy ngày thanh toán cuối cùng

#### Logic Tích Hợp Theo Đợt 3 Tháng (Ngày Bắt Đầu Bất Kỳ)
```sql
-- Sử dụng CROSS APPLY với function để tính các đợt thanh toán 3 tháng
-- Function này phải xử lý chính xác ngày bắt đầu bất kỳ
CROSS APPLY dbo.F_CalculateNgayThanhToanKyMoi(
    a.ngaythanhtoan_plhd,  -- Ngày bắt đầu có thể là bất kỳ ngày nào
    d.SoHD_PLHD, 
    d.DenNgay
) f

-- Tính tổng nợ tích lũy theo đợt đến cuối tháng T
WHERE f.NgayThanhToan <= @CuoiThangT
AND (ChiPhiTMB.NgayThanhToan IS NULL OR ChiPhiTMB.NgayThanhToan > f.NgayThanhToan)

-- LUU Y: Không thể dùng công thức đơn giản theo tháng dương lịch
-- VÍ DỤ SAI: CEILING(MONTH(f.NgayThanhToan) / 3.0) -- Chỉ đúng khi bắt đầu ngày 1
-- PHẢI DÙNG: Logic tính toán dựa trên ngaythanhtoan_plhd cụ thể

-- Ví dụ logic đúng cho đợt bắt đầu ngày 15:
-- Đợt 1: 15/01 -> 14/04
-- Đợt 2: 15/04 -> 14/07  
-- Đợt 3: 15/07 -> 14/10
-- Đợt 4: 15/10 -> 14/01+1
```

**Ghi chú Implementation:**
- Function `F_CalculateNgayThanhToanKyMoi` cần được kiểm tra kỹ lưỡng
- Đảm bảo xử lý đúng các trường hợp ngày bắt đầu khác nhau
- Test với nhiều ngày bắt đầu: 1, 15, 20, 28, 31 của tháng

## Kết Quả Mong Đợi Mới

Báo cáo sẽ cung cấp:
1. **Tổng công nợ tích lũy** tại thời điểm cuối tháng được chọn
2. **Phân loại theo trạng thái** PLHD (hoạt động, hết hạn, hủy, quá hạn)
3. **Chi tiết từng PLHD** với số tiền nợ cụ thể
4. **Phân biệt rõ ràng** giữa số liệu thực tế và lý thuyết
5. **Ưu tiên hiển thị** các PLHD quá hạn với màu đỏ
6. **Linh hoạt thời gian** - có thể xem tổng nợ tại cuối bất kỳ tháng nào
7. **Xu hướng nợ** - so sánh với các tháng trước để thấy xu hướng tăng/giảm nợ

## So Sánh Với Cách Hiểu Cũ

| Khía cạnh | Cách hiểu cũ (Period) | Cách hiểu mới (Point in Time) |
|-----------|----------------------|------------------------------|
| **Câu hỏi chính** | "Tháng này tôi phải trả bao nhiêu?" | "Đến cuối tháng này, tôi còn nợ bao nhiêu?" |
| **Phạm vi thời gian** | Chỉ trong tháng T | Từ quá khứ đến cuối tháng T |
| **Loại chi phí** | Chi phí phát sinh | Công nợ tích lũy |
| **Chu kỳ thanh toán** | Theo tháng | Theo đợt 3 tháng |
| **Mục đích** | Lập kế hoạch chi tiêu | Quản lý công nợ |
| **Kết quả** | Chi phí tháng hiện tại | Tổng nợ tại thời điểm |
| **Ví dụ** | "T8 phải trả 10 triệu" | "Cuối T8 còn nợ 30 triệu (1 đợt 3 tháng)" |
