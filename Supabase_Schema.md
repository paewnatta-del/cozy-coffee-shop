# ออกแบบฐานข้อมูล Supabase (Database Schema Design)
จากเอกสาร SRS สำหรับระบบรับจองโต๊ะร้านกาแฟ สามารถแบ่งการจัดเก็บข้อมูลออกเป็น 2 ตารางหลักใน Supabase ดังนี้ครับ

---

## 1. ตาราง `tables` (ข้อมูลโต๊ะและโซนที่นั่ง)
**หน้าที่:** เก็บ Master Data ของโต๊ะทั้งหมดภายในร้าน เพื่อใช้แสดงผลและตรวจสอบความจุสูงสุด

### Columns & Data Types
| Column Name | Data Type | Constraints / Default | Description |
|---|---|---|---|
| `id` | `uuid` | Primary Key, Default: `uuid_generate_v4()` | รหัสประจำตัวของโต๊ะ (Unique) |
| `table_code` | `text` | Not Null, Unique | รหัสโต๊ะ เช่น T01, OUT-01 |
| `zone_name` | `text` | Not Null | ชื่อโซน เช่น Indoor, Outdoor |
| `capacity` | `int2` (smallint) | Not Null, `> 0` | จำนวนที่นั่งสูงสุดที่รองรับได้ |
| `is_active` | `boolean` | Default: `true` | สถานะเปิดใช้งานโต๊ะ (true=เปิดรับจอง) |
| `created_at` | `timestamptz` | Default: `now()` | วันเวลาที่สร้างข้อมูล |

### ข้อมูลตัวอย่าง (5 แถว)
| id | table_code | zone_name | capacity | is_active | created_at |
|---|---|---|---|---|---|
| `uuid-1...` | T01 | Indoor | 2 | true | 2026-06-21 10:00:00 |
| `uuid-2...` | T02 | Indoor | 4 | true | 2026-06-21 10:00:00 |
| `uuid-3...` | T03 | Indoor | 4 | true | 2026-06-21 10:00:00 |
| `uuid-4...` | OUT-01 | Outdoor | 2 | true | 2026-06-21 10:00:00 |
| `uuid-5...` | OUT-02 | Outdoor | 6 | true | 2026-06-21 10:00:00 |

---

## 2. ตาราง `bookings` (ข้อมูลการจองโต๊ะ)
**หน้าที่:** เก็บรายการจองโต๊ะของลูกค้า รวมถึงสถานะการอนุมัติจากฝั่งพนักงาน

### Columns & Data Types
| Column Name | Data Type | Constraints / Default | Description |
|---|---|---|---|
| `id` | `uuid` | Primary Key, Default: `uuid_generate_v4()` | รหัสประจำรายการจอง (Unique) |
| `customer_name` | `text` | Not Null | ชื่อ-นามสกุลลูกค้า |
| `customer_phone`| `text` | Not Null | เบอร์โทรศัพท์สำหรับติดต่อ |
| `booking_date` | `date` | Not Null | วันที่ลูกค้าต้องการเข้ามา |
| `booking_time` | `time` | Not Null | เวลาที่ลูกค้าต้องการเข้ามา |
| `guests_count` | `int2` (smallint) | Not Null, `> 0` | จำนวนคนที่เข้าใช้งาน |
| `zone_preference`| `text` | Not Null | โซนที่ต้องการ (Indoor/Outdoor) |
| `status` | `text` | Default: `'pending'` | สถานะ: `pending`, `approved`, `rejected` |
| `created_at` | `timestamptz` | Default: `now()` | วันเวลาที่กดทำรายการจอง |

### ข้อมูลตัวอย่าง (5 แถว)
| id | customer_name | customer_phone | booking_date | booking_time | guests_count | zone_preference | status | created_at |
|---|---|---|---|---|---|---|---|---|
| `uuid-1...` | คุณสมชาย | 0812345678 | 2026-06-25 | 10:00:00 | 2 | Indoor | `pending` | 2026-06-21 11:30:00 |
| `uuid-2...` | คุณแพรว | 0898765432 | 2026-06-25 | 14:30:00 | 4 | Outdoor | `approved` | 2026-06-21 11:45:00 |
| `uuid-3...` | คุณเอ | 0823334444 | 2026-06-26 | 09:00:00 | 1 | Indoor | `pending` | 2026-06-21 12:00:00 |
| `uuid-4...` | คุณบี | 0811112222 | 2026-06-26 | 13:00:00 | 6 | Outdoor | `rejected` | 2026-06-21 12:15:00 |
| `uuid-5...` | คุณซี | 0855556666 | 2026-06-27 | 15:00:00 | 2 | Indoor | `approved` | 2026-06-21 12:30:00 |

---

## 💡 คำแนะนำการอ่าน/เขียนข้อมูลบน Frontend

### สำหรับฝั่งลูกค้า (Customer UI)
1. **การเขียน (Insert):** 
   - เมื่อลูกค้ากดปุ่ม "ยืนยันการจอง" ให้ Frontend ทำการยิงคำสั่ง `supabase.from('bookings').insert([...])`
   - ค่า `status` ไม่ต้องส่งไปจาก Frontend ปล่อยให้ Supabase จัดการใช้ Default เป็น `'pending'` เพื่อความปลอดภัย
   - ค่า `created_at` ไม่ต้องส่ง ปล่อยให้ระบบบันทึกเวลาปัจจุบันอัตโนมัติ

2. **การอ่าน (Select):** 
   - หน้าจองอาจจะไม่มีการอ่านข้อมูลจาก Database (ยกเว้นแต่จะมีการดึงข้อมูล Master Data โซนที่นั่งจากตาราง `tables` มาสร้างตัวเลือก Dropdown/Radio เพื่อให้ข้อมูลตรงกัน)

### สำหรับฝั่งแอดมิน (Admin UI)
1. **การอ่าน (Select):**
   - หน้า Dashboard: ใช้คำสั่ง `supabase.from('bookings').select('*').order('booking_date', { ascending: true })` เพื่อดึงคิวเรียงตามวันที่จอง
   - หน้า Status โต๊ะ: อ่านข้อมูล `tables` มาวนลูปแสดงผล ควบคู่กับการนับจำนวนจองใน `bookings` ที่ `status = 'approved'` และตรงกับเวลาปัจจุบัน

2. **การเขียน/อัปเดต (Update):**
   - เมื่อแอดมินกดปุ่ม ✅ อนุมัติ: ให้ Frontend ยิงคำสั่งอัปเดตสถานะเป็น `approved` `supabase.from('bookings').update({ status: 'approved' }).eq('id', booking_id)`
   - เมื่อแอดมินกดปุ่ม ❌ ยกเลิก: ให้ยิงคำสั่งอัปเดตสถานะเป็น `rejected` `supabase.from('bookings').update({ status: 'rejected' }).eq('id', booking_id)`
