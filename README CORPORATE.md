# NIGIWAI GROUP — Corporate Intelligence Dashboard

> ระบบ Dashboard สำหรับติดตามข้อมูลบริษัทในเครือ Nigiwai Group แบบ Real-time  
> เชื่อมต่อกับ Google Sheets ผ่าน Google Apps Script

---

## สารบัญ

- [ภาพรวมโครงการ](#ภาพรวมโครงการ)
- [คุณสมบัติหลัก](#คุณสมบัติหลัก)
- [โครงสร้างข้อมูล](#โครงสร้างข้อมูล)
- [การติดตั้งและตั้งค่า](#การติดตั้งและตั้งค่า)
- [โครงสร้างโค้ด](#โครงสร้างโค้ด)
- [การใช้งาน](#การใช้งาน)
- [Admin Panel](#admin-panel)
- [Demo Mode](#demo-mode)
- [Dependencies](#dependencies)

---

## ภาพรวมโครงการ

**Nigiwai Group Corporate Intelligence Dashboard** คือ Single-Page Application (SPA) ที่สร้างด้วย HTML/CSS/JavaScript ล้วน ไม่ต้องพึ่ง framework ใด ออกแบบมาเพื่อให้ผู้บริหารสามารถมองเห็นข้อมูลสำคัญขององค์กรได้ในหน้าเดียว ได้แก่:

- โครงสร้างผู้ถือหุ้นของบริษัทในเครือทั้งหมด
- ทุนจดทะเบียน / ทุนที่ชำระแล้ว / หนี้สิน
- กรรมการผู้มีอำนาจลงนาม (ก่อนและหลังปรับ)
- รายการค้างดำเนินการ (Issues) แยกตามบริษัท
- ดัชนีสุขภาพองค์กร (Corporate Health Index)

ข้อมูลทั้งหมดดึงจาก **Google Sheets** ผ่าน **Google Apps Script REST API** และสามารถแก้ไขข้อมูลกลับไปยัง Sheet ได้โดยตรงจากหน้า Admin

---

## คุณสมบัติหลัก

### Dashboard (สาธารณะ)

| คุณสมบัติ | รายละเอียด |
|---|---|
| **Company Selector** | กรองข้อมูลตามบริษัท หรือดูภาพรวมทุกบริษัทในเครือ |
| **Company Info Strip** | แสดง เลขนิติบุคคล / วันจดทะเบียน / อายุบริษัท / ที่อยู่ แบบ Real-time |
| **KPI Cards (5 ใบ)** | บริษัทในเครือ · ทุนรวม · หนี้สินรวม · ผู้ถือหุ้น · รายการค้าง — คลิกเพื่อดูรายละเอียด |
| **Priority Alerts** | แสดงรายการที่ต้องดำเนินการด่วน เรียงลำดับความสำคัญ |
| **Health Metrics** | Progress bar ติดตาม 4 มิติ: ที่อยู่, ทุนชำระ, กรรมการ, หนี้สิน |
| **Ownership Chart** | Doughnut chart (รายบริษัท) / Horizontal bar (ภาพรวม % Nigiwai) |
| **Capital Chart** | Stacked bar แสดงทุนชำระแล้ว vs ค้างชำระ |
| **Debt Chart** | Bar chart หนี้สินแยกบริษัท เรียงมากไปน้อย |
| **Directors Table** | ตารางกรรมการผู้มีอำนาจ พร้อม sort ได้ทุกคอลัมน์ |
| **Shareholders Table** | 2 โหมด: Grouped (จัดกลุ่มตามบริษัท) และ Flat (ทะเบียนรวม พร้อม sort) |

### Admin Panel (ต้องใช้รหัสผ่าน)

| Tab | คุณสมบัติ |
|---|---|
| **ข้อมูลบริษัท** | แก้ไขข้อมูล Company-level: ที่อยู่, กรรมการ, หมายเหตุ — อัปเดตทุก row พร้อมกัน |
| **ผู้ถือหุ้น** | เพิ่ม / แก้ไข / ลบ ข้อมูลผู้ถือหุ้นรายบุคคล |
| **กรรมการ** | แก้ไขรายชื่อกรรมการก่อน/หลังปรับ ด้วย Dynamic input rows |

---

## โครงสร้างข้อมูล

ข้อมูลถูกเก็บใน Google Sheets โดยแต่ละแถว (row) แทน **ผู้ถือหุ้น 1 ราย ใน 1 บริษัท**

### คอลัมน์ทั้งหมด

```
ลำดับ
ชื่อบริษัท
เลขนิติบุคคล
วันที่จดทะเบียน
ทุนจดทะเบียน (บาท)
ชื่อผู้ถือหุ้น
จำนวนหุ้น
เงินลงทุน (บาท)
เงินค้างชำระ (บาท)
ทุนที่ชำระแล้ว (%)
จำนวนทุนที่ชำระแล้ว (บาท)
หนี้สิน (บาท)
กรรมการผู้มีอำนาจ (ก่อนปรับ)
กรรมการผู้มีอำนาจ (หลังปรับ)
ผู้ถือหุ้นใหม่
ที่อยู่สำนักงานใหญ่ปัจจุบัน
ที่อยู่สำนักงานใหญ่ใหม่
หมายเหตุ
ปัญหา
สถานะอัพเดท
```

> **หมายเหตุ:** ข้อมูล Company-level (เช่น ทุนจดทะเบียน, กรรมการ, ที่อยู่) จะซ้ำกันในทุก row ของบริษัทเดียวกัน  
> ระบบจะ aggregate และ deduplicate ข้อมูลเหล่านี้โดยอัตโนมัติเมื่อแสดงผล

---

## การติดตั้งและตั้งค่า

### 1. Deploy Google Apps Script

สร้าง Apps Script ที่รองรับ endpoints ต่อไปนี้:

```javascript
// GET Actions
?action=getAll          // ดึงข้อมูลทั้งหมด

// POST Actions (ต้องมี password ใน body)
{ action: 'add',                data: {...} }
{ action: 'update',             rowIndex: N, data: {...} }
{ action: 'delete',             rowIndex: N }
{ action: 'updateAllForCompany', companyName: '...', fields: {...} }
```

Response format:
```json
{ "success": true, "data": [...] }
{ "success": false, "error": "error message" }
```

### 2. ตั้งค่า API_URL

เปิดไฟล์ `index.html` และแก้ไขค่าที่บรรทัด:

```javascript
const API_URL = 'https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec';
```

แทนที่ด้วย URL ที่ได้จากการ Deploy Apps Script ของคุณ

### 3. ตั้งค่ารหัสผ่าน Admin

รหัสผ่านจะถูก validate ฝั่ง Google Apps Script เมื่อมี POST request เข้ามา ให้กำหนดในโค้ด Apps Script ของคุณ

### 4. รัน

ไม่ต้องติดตั้ง dependencies เพิ่มเติม — เปิดไฟล์ `index.html` ในเบราว์เซอร์ได้เลย หรือ host บน GitHub Pages / Firebase Hosting / Cloudflare Pages

---

## โครงสร้างโค้ด

ไฟล์เดียว `index.html` แบ่งเป็นส่วนต่าง ๆ ดังนี้:

```
index.html
│
├── <style>          CSS Variables, Layout, Components
│
├── <body>
│   ├── #appHeader          Header + Navigation
│   ├── #filterBar          Filter Bar (Refresh, Record count)
│   ├── #companyInfoStrip   Company Info Strip (แสดงเมื่อเลือกบริษัท)
│   │
│   ├── #pgDashboard        หน้า Dashboard
│   │   ├── #companySelectorHero    Company Dropdown
│   │   ├── #kpiGrid                KPI Cards
│   │   ├── Priority Alerts + Health Metrics
│   │   ├── Charts (Ownership, Capital, Debt)
│   │   ├── Directors Table
│   │   └── Shareholders Table
│   │
│   └── #pgAdmin            หน้า Admin
│       ├── Tab: ข้อมูลบริษัท
│       ├── Tab: ผู้ถือหุ้น
│       └── Tab: กรรมการ
│
├── Modals
│   ├── #loginModal         Login
│   ├── #editModal          Edit Shareholder
│   ├── #companyEditModal   Edit Company
│   ├── #directorEditModal  Edit Director
│   └── #kpiDetailModal     KPI Drilldown
│
└── <script>
    ├── ① CONFIG            API_URL
    ├── ② DEMO_DATA         ข้อมูลตัวอย่าง (32 rows, 12 บริษัท)
    ├── ③ FIELDS            Field definitions สำหรับ forms
    ├── ④ STATE             Global state object
    ├── ⑤ INIT              loadData()
    ├── ⑥ FILTER            Company filter, Info strip
    ├── ⑦ DASHBOARD         KPIs, Charts, Tables
    ├── ⑧ ADMIN             CRUD operations
    ├── ⑨ UI HELPERS        Modal, Toast, Page navigation
    └── ⑩ FORMAT HELPERS    fmt, fmtTHB, shortName
```

### State Object

```javascript
const state = {
  allData: [],          // ข้อมูลทั้งหมดจาก API
  filteredData: [],     // ข้อมูลหลังกรองตามบริษัท
  selectedCompany: 'all',
  isAdminLoggedIn: false,
  adminPassword: '',
  charts: {},           // Chart.js instances (ownership, capital, debt)
  isDemo: false,        // true = ใช้ DEMO_DATA
  addFormOpen: false,
  adminView: 'company', // 'company' | 'shareholder' | 'director'
  shView: 'grouped',    // 'grouped' | 'flat'
  shSort: { col: null, dir: 1 },
  dirSort: { col: 'age', dir: -1 },
};
```

---

## การใช้งาน

### เลือกบริษัท

- เลือก **"ทุกบริษัทในเครือ"** เพื่อดูภาพรวม
- เลือกบริษัทใดบริษัทหนึ่งเพื่อดูข้อมูลเฉพาะ — Company Info Strip จะปรากฏพร้อม อายุบริษัท, เลขนิติบุคคล, ที่อยู่

### KPI Cards

คลิกที่ KPI Card ใด ๆ เพื่อเปิด Drilldown Modal แสดงรายละเอียดเต็ม:

| KPI | Drilldown แสดง |
|---|---|
| บริษัทในเครือ | ตารางบริษัททั้งหมดพร้อมกรรมการ |
| ทุนจดทะเบียน | ทุน / ชำระแล้ว / ค้างชำระ / หนี้สิน ต่อบริษัท |
| หนี้สินรวม | บริษัทที่มีหนี้ เรียงมากไปน้อย |
| ผู้ถือหุ้น | ทะเบียนผู้ถือหุ้นทั้งหมด (ไม่ซ้ำ) |
| รายการค้าง | บริษัทและรายการค้างดำเนินการทั้งหมด |

### ตารางผู้ถือหุ้น

- **โหมด Grouped:** จัดกลุ่มตามบริษัท แสดง Summary row ต่อท้ายแต่ละบริษัท
- **โหมด Flat:** แสดงทุก row พร้อม sort ได้ทุกคอลัมน์ (คลิก header)

### ตารางกรรมการ

- Sort ได้ด้วยคอลัมน์ `#`, `ชื่อบริษัท`, `อายุบริษัท`, `ผู้ถือหุ้นใหม่`, `สถานะ`
- Default sort: เรียงตาม **อายุบริษัท มากไปน้อย** (บริษัทเก่าสุดขึ้นก่อน)

---

## Admin Panel

### เข้าสู่ระบบ

คลิกปุ่ม **Admin** ในแถบ Navigation → กรอกรหัสผ่าน

### Tab: ข้อมูลบริษัท

- แก้ไขข้อมูล Company-level ทั้งหมด
- เมื่อบันทึก ระบบจะอัปเดต **ทุก row** ของบริษัทนั้นใน Google Sheets พร้อมกัน
- ใช้ `action: updateAllForCompany`

### Tab: กรรมการ

- เพิ่ม/ลบ กรรมการแบบ Dynamic (ไม่จำกัดจำนวน)
- แยกชัดเจนระหว่าง "ก่อนปรับ" และ "หลังปรับ/ปัจจุบัน"
- บันทึกในรูปแบบ `ชื่อ1 / ชื่อ2 / ชื่อ3`

### Tab: ผู้ถือหุ้น

- เพิ่มผู้ถือหุ้นใหม่ด้วย Collapsible form
- แก้ไข / ลบ รายบุคคล

---

## Demo Mode

เมื่อ `API_URL` ยังไม่ได้ตั้งค่า ระบบจะใช้ `DEMO_DATA` อัตโนมัติ:

- ข้อมูลตัวอย่าง **12 บริษัท, 32 ผู้ถือหุ้น**
- การแก้ไขใน Admin จะอัปเดต Local State เท่านั้น (ไม่บันทึกลง Sheet จริง)
- แจ้งเตือนด้วย Toast: `"ใช้ข้อมูล Demo"`

---

## Dependencies

โหลดทั้งหมดจาก CDN — ไม่ต้องติดตั้งเพิ่ม

| Library | Version | การใช้งาน |
|---|---|---|
| [Tailwind CSS](https://tailwindcss.com) | latest | Utility classes บางส่วน |
| [Chart.js](https://chartjs.org) | 4.4.0 | Doughnut, Bar charts |
| [chartjs-plugin-datalabels](https://chartjs-plugin-datalabels.netlify.app) | 2.2.0 | Labels บน charts |
| [Font Awesome](https://fontawesome.com) | 6.5.0 | Icons |
| [Google Fonts](https://fonts.google.com) | — | Noto Sans Thai, Noto Serif Thai, Inter, IBM Plex Mono |

> ระบบทำงานได้ **100% ฝั่ง Client** ไม่มี Server-side rendering ไม่ต้อง Node.js ไม่ต้อง Build step

---

## สิทธิ์การเข้าถึง

| ส่วน | การเข้าถึง |
|---|---|
| Dashboard | สาธารณะ (ไม่ต้อง Login) |
| Admin Panel | ต้องใช้รหัสผ่าน (validate ที่ Google Apps Script) |
| Google Sheets | ต้อง Deploy Apps Script ด้วยสิทธิ์ที่เหมาะสม |

---

*Nigiwai Group Corporate Intelligence — Internal Use Only*
