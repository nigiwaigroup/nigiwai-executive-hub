# 🏢 Nigiwai Group — HR & Manpower Cost Dashboard 2026

ระบบ Dashboard บริหารข้อมูลพนักงานและงบประมาณบุคลากร สำหรับกลุ่มบริษัท Nigiwai Group  
พัฒนาด้วย **Vanilla HTML/CSS/JS** + **Google Apps Script** — Deploy บน GitHub Pages ได้ทันที ไม่ต้องมี Backend

---

## 📁 โครงสร้างไฟล์

```
nigiwai-hr-dashboard/
│
├── index.html        # หน้า Dashboard (สาธารณะ)
├── admin.html        # หน้าจัดการข้อมูล (ต้อง Login)
├── code.gs           # Google Apps Script API
└── README.md         # ไฟล์นี้
```

---

## ✨ Features

### 📊 หน้า Dashboard (`index.html`)

| Tab | รายละเอียด |
|-----|-----------|
| 🏢 ภาพรวม | KPI Cards, HBar Charts แยก Cost Center / แผนก, Donut Chart ระดับตำแหน่ง |
| 👥 Headcount | ตารางพนักงานทั้งหมด, Filter, **Sort ทุก Column** (คลิกหัว Column) |
| 📋 งบฯ ตาม Cost Center | YoY Bar Chart ปี 68 vs 69, รายชื่อพนักงานแยก CC |
| 🔄 Internal Mobility | Flow Chart โอนออก/โอนเข้า, รายละเอียดการย้ายพร้อม History |
| 📊 Payroll Distribution | 4 กราฟแนวนอน: CC, แผนก, YoY เปรียบเทียบ, Donut ระดับ |
| 💰 ปรับเงินเดือน #01/26 | **🔐 ต้องใส่ Password** — สรุปการปรับเงินเดือน |

**เมนูปรับแต่งได้** จาก Admin → จัดการเมนู (เปิด/ปิด/เรียงลำดับ/แก้ชื่อ)

---

### ⚙️ หน้า Admin (`admin.html`)

**Login:** `admin` / `nigiwai2026` หรือ `hr` / `hrpass2026`

| Tab | รายละเอียด |
|-----|-----------|
| 👤 พนักงาน | ตาราง Inline Edit, Filter, Search, บันทึก → Google Sheets ทันที |
| 🔄 Mobility | ค้นหา + Filter, บันทึกการโยกย้าย, แสดง History ย้อนหลัง |
| 🏢 Cost Center | เพิ่ม/แก้ไข/ลบ บริษัทในกลุ่ม (10 บริษัท) |
| ⚙ จัดการเมนู | Toggle เปิด/ปิดเมนู, Drag & Drop เรียงลำดับ, แก้ชื่อเมนู |

---

## 🏗️ บริษัทในกลุ่ม (Cost Centers)

| Key | ชื่อเต็ม |
|-----|---------|
| Nigiwai Goods Wealth | บริษัท นิกิวาอิ กู้ดส์ เวลท์ จำกัด (สำนักงานใหญ่) |
| Nigiwai Group | บริษัท นิกิวาอิ กรุ๊ป จำกัด (สำนักงานใหญ่) |
| Nigiwai Construction | บริษัท นิกิวาอิ คอนสตรัคชั่น จำกัด (สำนักงานใหญ่) |
| NGWC HQ | บริษัท เอ็นจีดับบลิวซี จำกัด (สำนักงานใหญ่) |
| NGWC Don Mueang | บริษัท เอ็นจีดับบลิวซี จำกัด (002 สนามบินดอนเมือง) |
| NGWC Phuket | บริษัท เอ็นจีดับบลิวซี จำกัด (003 สนามบินภูเก็ต) |
| Kinosuke Group | บริษัท คินโนะสุเกะ กรุ๊ป จำกัด (สำนักงานใหญ่) |
| Phuket Ocean | บริษัท ภูเก็ตโอเชี่ยนกรุ๊ป จำกัด (The Oceanic Sportel) |
| Viang Viet | บริษัท เวียงเวียต ฟู้ด จำกัด (สำนักงานใหญ่) |
| Andamanda | บริษัท สุดสาครกรุ๊ป จำกัด (Andamanda) |

---

## 🔌 Google Apps Script API (`code.gs`)

### Deploy Settings

```
Type:             Web app
Execute as:       Me
Who has access:   Anyone   ← ⚠️ ต้องเป็น "Anyone" เท่านั้น
```

### Endpoints

**GET** `?callback=fn&data=JSON` — JSONP (ใช้จาก browser)

**POST** `body: JSON` — Direct POST

| Action | คำอธิบาย |
|--------|---------|
| `ping` | ทดสอบการเชื่อมต่อ + ดึง Summary |
| `upsert` | เพิ่ม/อัปเดต พนักงาน 1 คน (by `id`) |
| `delete` | ลบพนักงาน 1 คน (by `id`) |
| `sync` | Clear ทั้งหมด แล้วเขียน Batch แรก |
| `append` | เพิ่ม Batch ถัดไป (ใช้คู่กับ `sync`) |
| `getAll` | ดึงข้อมูลพนักงานทั้งหมด |
| `getById` | ดึงข้อมูลพนักงาน 1 คน |
| `summary` | สรุป Headcount + รายได้ แยก CC |

> **หมายเหตุ:** Full Sync ส่งทีละ 3 คน (Chunk) เพราะ JSONP มี URL limit ~7,500 chars

### Google Sheet Structure

Sheet Name: **`HR_Data`**

| Column | Field | คำอธิบาย |
|--------|-------|---------|
| A | id | รหัสพนักงาน |
| B | name | ชื่อ-นามสกุล |
| C | nickname | ชื่อเล่น |
| D | brand | Cost Center key |
| E | dept | แผนก |
| F | position | ตำแหน่ง |
| G | level | ระดับ |
| H | baseSalary68 | ฐานเงินเดือน ปี 68 |
| I | baseSalary69 | ฐานเงินเดือน ปี 69 |
| J | totalIncome68 | รายได้รวม ปี 68 |
| K | totalIncome69 | รายได้รวม ปี 69 |
| L | remark | หมายเหตุ |
| M | brandOld | บริษัทเดิม (ชื่อเต็ม) |
| N | brandNew | บริษัทปัจจุบัน (ชื่อเต็ม) |
| O | moveDate | วันที่ย้าย |
| P | moveHistory | ประวัติการย้าย (JSON Array) |
| Q | updatedAt | เวลาอัปเดตล่าสุด |

---

## 🚀 วิธี Deploy บน GitHub Pages

```bash
# 1. Clone หรือ init repo
git init
git add .
git commit -m "Initial HR Dashboard"
git remote add origin https://github.com/YOUR_USERNAME/nigiwai-hr-dashboard.git
git push -u origin main

# 2. เปิด GitHub Pages
# Settings → Pages → Source: Deploy from branch → main → / (root) → Save
```

URL จะเป็น: `https://YOUR_USERNAME.github.io/nigiwai-hr-dashboard/`

---

## ⚙️ วิธีตั้งค่าครั้งแรก

### Step 1 — Setup Google Apps Script

1. เปิด Google Sheet ใหม่
2. **Extensions → Apps Script**
3. ลบโค้ดเดิมทั้งหมด → วาง `code.gs` → กด Save
4. **Deploy → New deployment**
   - Type: `Web app`
   - Execute as: `Me`
   - Who has access: `Anyone`
5. กด **Deploy** → Authorize → **Copy URL** (ลงท้าย `/exec`)

### Step 2 — อัปเดต GAS URL ใน admin.html

เปิด `admin.html` แก้บรรทัดนี้:

```javascript
var GAS_URL = 'https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec';
```

### Step 3 — Sync ข้อมูลขึ้น Sheet

1. เปิด `admin.html` → Login
2. กด **🔍 Test GAS** — ตรวจสอบการเชื่อมต่อ
3. กด **☁ Full Sync** — โหลดข้อมูลพนักงาน 53 คนขึ้น Sheet

> **Re-deploy ทุกครั้งที่แก้ code.gs:**  
> Apps Script → Deploy → Manage deployments → Edit (✏) → New version → Deploy

---

## 🔧 Configuration

### เปลี่ยน Admin Password

แก้ใน `admin.html`:

```javascript
var USERS = {
  admin: 'nigiwai2026',   // ← เปลี่ยนได้
  hr:    'hrpass2026'     // ← เปลี่ยนได้
};
```

### เปลี่ยน Password หน้าเงินเดือน

แก้ใน `index.html`:

```javascript
const PAYROLL_PASS = 'nigiwai2026';  // ← เปลี่ยนได้
```

### เพิ่ม Cost Center

เปิด `admin.html` → Login → Tab **🏢 Cost Center** → กด **+ เพิ่ม**

---

## 🗄️ Data Storage

ข้อมูลเก็บใน 2 ที่พร้อมกัน:

| ที่เก็บ | คำอธิบาย |
|--------|---------|
| `localStorage` | ข้อมูลใน Browser (เร็ว, ใช้แบบ Offline ได้) |
| Google Sheets | Cloud Storage (Sync อัตโนมัติทุกครั้งที่แก้ไข) |

**Keys ที่ใช้ใน localStorage:**

```
hr_db              — ข้อมูลพนักงาน (Array of employees)
hr_cc              — ข้อมูล Cost Center
hr_menu_config     — การตั้งค่าเมนู Dashboard
hr_dashboard_config — การตั้งค่าทั่วไป
hr_last_save       — Timestamp ล่าสุดที่บันทึก
```

---

## 📐 Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | HTML5, CSS3, JavaScript (Vanilla) |
| Charts | Chart.js 4.4.0 |
| Fonts | Google Fonts — Sarabun, Prompt |
| Backend | Google Apps Script (Web App) |
| Database | Google Sheets |
| Hosting | GitHub Pages |

**ไม่มี Dependencies ที่ต้อง Install** — ใช้ CDN ทั้งหมด

---

## 📱 Responsive

รองรับทุก Device:

- 🖥️ Desktop (1100px+)
- 📱 Tablet (768px)
- 📱 Mobile (480px)

---

## 🎨 CI Color Scheme

**Nigiwai Group Corporate Identity:**

| Color | Hex | ใช้สำหรับ |
|-------|-----|---------|
| Red | `#c0392b` | Primary accent, borders |
| Gold | `#c9a227` | Secondary accent, highlights |
| Black | `#1a1a1a` | Topbar background |
| White | `#ffffff` | Card backgrounds |
| Light Gray | `#f2f3f5` | Page background |

---

## 📋 ข้อมูล Seed (53 พนักงาน)

ข้อมูลเริ่มต้นฝังไว้ใน `index.html` และ `admin.html` เป็น JSON Seed  
พนักงานครอบคลุม 6 Cost Center หลัก ปีงบประมาณ 2568–2569 (2025–2026)

**ระดับตำแหน่ง:**
- Officer
- Senior Officer
- Leader and Supervisor
- Manager
- Chief and Director
- Consultant

---

## 🔄 Internal Mobility Logic

- `brand` — Cost Center ปัจจุบัน (key)
- `brandOld` — ชื่อเต็มบริษัทแรกสุดที่สังกัด (ไม่เปลี่ยนแปลง)
- `brandNew` — ชื่อเต็มบริษัทปัจจุบัน (อัปเดตทุกครั้งที่ย้าย)
- `moveHistory` — Array ของ `{ from, to, date }` เก็บทุก step

```json
{
  "brandOld": "บริษัท คินโนะสุเกะ กรุ๊ป จำกัด (สำนักงานใหญ่)",
  "brandNew": "บริษัท เอ็นจีดับบลิวซี จำกัด (003 สนามบินภูเก็ต)",
  "moveDate": "17/03/2026",
  "moveHistory": [
    {
      "from": "บริษัท คินโนะสุเกะ กรุ๊ป จำกัด (สำนักงานใหญ่)",
      "to":   "บริษัท เอ็นจีดับบลิวซี จำกัด (003 สนามบินภูเก็ต)",
      "date": "17/03/2026"
    }
  ]
}
```

---

## 🐛 Known Issues & Notes

- **Full Sync ช้า** — ส่งทีละ 3 คน (53 คน ≈ 18 requests) เพราะ JSONP URL limit
- **Offline Mode** — ถ้า GAS ไม่ตอบสนอง ข้อมูลใน localStorage ยังใช้งานได้ปกติ
- **Re-deploy GAS** — ต้องทำทุกครั้งที่แก้ `code.gs` มิฉะนั้น GAS รันโค้ดเก่า

---

## 📞 ติดต่อ / ดูแลระบบ

**Nigiwai Group**  
ระบบนี้พัฒนาสำหรับใช้งานภายในองค์กร  
ข้อมูลพนักงานและการเงินเป็นความลับ — ห้ามเผยแพร่สู่สาธารณะ

---

*Last updated: March 2026 | HR Dashboard v3.1*
