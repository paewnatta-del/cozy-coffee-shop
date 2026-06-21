# ออกแบบ User Flow และ Wireframe: ระบบรับจองโต๊ะร้านกาแฟ

อ้างอิงจาก SRS ระบบรับจองโต๊ะร้านกาแฟ ด้านล่างนี้คือรายการหน้าจอ User Flow และโครงสร้าง Wireframe ครับ

---

## 1. รายการหน้าจอที่ต้องมี (Screens List)

### ฝั่งลูกค้า (Customer)
1. **หน้า Landing Page (`/`)**: หน้าแรกสำหรับให้ข้อมูลร้านค้า บรรยากาศ และเป็นจุดเริ่มต้นในการกดเข้าสู่ระบบจอง
2. **หน้า Booking Form (`/book`)**: ฟอร์มสำหรับให้ลูกค้าเลือก วัน เวลา โซน จำนวนคน และกรอกข้อมูลผู้ติดต่อ
3. **หน้า Booking Success (`/success`)**: หน้าแสดงผลลัพธ์หลังการจองสำเร็จ พร้อมสรุปรายละเอียด และแจ้งสถานะ (รอดำเนินการ)

### ฝั่งแอดมิน (Admin)
4. **หน้า Admin Login (`/admin/login`)**: หน้าสำหรับพนักงานเข้าสู่ระบบ
5. **หน้า Admin Dashboard (`/admin/dashboard`)**: หน้าหลักที่แสดงภาพรวม เช่น จำนวนการจองวันนี้ และสถานะโต๊ะปัจจุบัน
6. **หน้า Booking Management (`/admin/bookings`)**: ตารางแสดงรายการจองทั้งหมดที่รอการอนุมัติ (Approve/Reject)

---

## 2. User Flow การใช้งาน

**Customer Flow (ลูกค้า):**
```text
[Landing Page] 
   └── (คลิกปุ่ม "จองโต๊ะ") ──> [Booking Form]
                                ├── (เลือกระบุ วัน, เวลา, จำนวนคน, โซน)
                                ├── (กรอก ชื่อ, เบอร์โทร)
                                └── (กดปุ่ม "ยืนยันการจอง") ──> [Booking Success] (รอการอนุมัติ)
```

**Admin Flow (พนักงาน):**
```text
[Admin Login] 
   └── (เข้าสู่ระบบ) ──> [Admin Dashboard]
                           ├── (ดูสถานะโต๊ะปัจจุบัน)
                           └── (คลิกดูรายการที่รอดำเนินการ) ──> [Booking Management]
                                                                ├── (เลือกรายการจอง)
                                                                └── (กดปุ่ม "อนุมัติ" หรือ "ยกเลิก")
```

---

## 3. Wireframe แบบข้อความ (Text Wireframe)

### 3.1 หน้า Landing Page (Customer)
```text
==================================================
[Header] โลโก้ร้าน | เมนู: หน้าแรก, เมนูแนะนำ, จองโต๊ะ
--------------------------------------------------
[Hero Section]
(ภาพพื้นหลังบรรยากาศร้านกาแฟ โทนน้ำตาลสว่าง)
ข้อความ: "พื้นที่อบอุ่น สำหรับคนรักกาแฟ"
ปุ่ม: [ จองโต๊ะเลย ]
--------------------------------------------------
[Info Section]
- เวลาเปิด-ปิด: 08.00 - 18.00 น.
- ข้อมูลการติดต่อเบื้องต้น
==================================================
```

### 3.2 หน้า Booking Form (Customer)
```text
==================================================
[Header] โลโก้ร้าน (คลิกกลับหน้าแรกได้)
--------------------------------------------------
[Form Container]
หัวข้อ: "ระบุรายละเอียดการจองโต๊ะ"

(ส่วนที่ 1: ข้อมูลโต๊ะ)
- เลือกวันที่: [ Date Picker ]
- เลือกเวลา: [ Time Picker ]
- จำนวนคน: [ Dropdown 1-10 คน ]
- เลือกโซน: ( ) Indoor  ( ) Outdoor

(ส่วนที่ 2: ข้อมูลติดต่อ)
- ชื่อผู้จอง: [ Text Input ]
- เบอร์โทรศัพท์: [ Text Input ]

ปุ่ม: [ ยืนยันการจอง ]   |   ปุ่ม: [ ยกเลิก ]
==================================================
```

### 3.3 หน้า Admin Dashboard & Management (Admin)
```text
==================================================
[Sidebar] 
- Dashboard (ภาพรวม)
- Bookings (จัดการการจอง)
- Logout
--------------------------------------------------
[Main Content - Bookings]
หัวข้อ: "รายการจองโต๊ะทั้งหมด"
[ ตารางแสดงข้อมูล ]
| ชื่อลูกค้า | เบอร์โทร | วัน-เวลา | โซน | สถานะ | จัดการ |
|---------|--------|--------|----|------|------|
| คุณเอ    | 081... | 14:00  | In | รอ   | [v][x] |
| คุณบี    | 089... | 15:30  | Out| รอ   | [v][x] |

* ปุ่ม [v] = อนุมัติ (Approve)
* ปุ่ม [x] = ยกเลิก (Reject)
==================================================
```

---

## 4. Prompt สำหรับสั่ง AI สร้าง Frontend (หน้าหลัก/Booking Form)

หากต้องการนำไปสั่ง AI (เช่น Claude, ChatGPT หรือ V0) ให้สร้าง Frontend คุณสามารถใช้ Prompt นี้ได้ครับ:

> **Prompt:**
> "Act as an expert Frontend Developer. Please create a responsive web page for a Coffee Shop Table Booking System using HTML, CSS (Vanilla or Tailwind), and simple JavaScript. 
> 
> **Theme & Aesthetics:**
> - Style: Warm & Cozy, modern, clean, premium.
> - Colors: Light brown, beige, caramel, warm earth tones.
> - Typography: Clean sans-serif fonts.
> 
> **Components to build:**
> 1. **Hero Section:** Full width background image of a cozy coffee shop, large welcoming headline, and a prominent 'Book a Table' CTA button.
> 2. **Booking Form Modal/Section:** When the user clicks 'Book a Table', display a card form containing:
>    - Date picker
>    - Time picker
>    - Guests count (dropdown)
>    - Seating Zone (Radio buttons: Indoor / Outdoor)
>    - Name input
>    - Phone number input
>    - Submit button ('Confirm Booking')
> 
> Please ensure the design includes subtle hover effects, micro-animations, and looks great on both mobile and desktop. Do not include backend logic, just the UI structure."
