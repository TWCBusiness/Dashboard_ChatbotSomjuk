# LINE Somjuk Stock Dashboard

เว็บแอปไฟล์เดียว (`index.html`) ไม่ต้อง build ไม่มี dependency อ่านข้อมูลจาก Google Sheet ผ่าน Apps Script (`Code.gs`) โหลดเองทันทีที่เปิดหน้า ไม่ต้องกดเชื่อมต่อ

## 1. ตั้งค่า Apps Script (ทำโดยบัญชีที่เข้าถึงชีตได้)

1. เปิด Google Sheet > Extensions > Apps Script
2. วางโค้ดจาก `Code.gs` แทนของเดิม (ตรวจ `SHEET_ID`, ถ้าข้อมูลอยู่แท็บอื่นให้ใส่ `SHEET_NAME`)
3. Deploy > New deployment > ประเภท Web app
   - Execute as: **Me**
   - Who has access: **Anyone**
4. กด Deploy และอนุญาตสิทธิ์ แล้วคัดลอก Web app URL (ลงท้าย `/exec`)
5. แก้โค้ดในชีตภายหลัง ต้อง Deploy > Manage deployments > แก้ไข > New version จึงจะมีผล

## 2. ตั้งค่าเว็บ

เปิด `index.html` หาบรรทัด `const API_URL=""` ใกล้ต้น `<script>` แล้วใส่ URL จากข้อ 1 (ถ้าตั้ง `API_KEY` ใน Code.gs ให้ใส่ค่าเดียวกันที่ `API_KEY` ในเว็บ)

## 3. ขึ้น GitHub Pages

1. อัปโหลด `index.html` (และ `README.md`) ไปที่ root ของ repository
2. Settings > Pages > Deploy from a branch > `main` / `(root)`
3. เปิด `https://<username>.github.io/<repo>/`

## ฟีเจอร์ที่ควรรู้

- **อัปเดตอัตโนมัติ:** เปิดหน้าไว้ เว็บจะตรวจข้อมูลใหม่ทุก 2 นาที (และเมื่อกลับมาที่แท็บ) ถ้าข้อมูลไม่เปลี่ยนจะไม่วาดใหม่ ตัวกรองและรายการที่เปิดดูอยู่จะไม่หาย ไม่ใช่ realtime แบบ push แต่ล่าช้าไม่เกิน ~2 นาที
- **Cache ฝั่ง Apps Script:** `Code.gs` เก็บผลลัพธ์ไว้ 120 วินาที (`CACHE_SECONDS`) ทำให้โหลดเร็วขึ้นเมื่อมีผู้ใช้หลายคน หลังแก้ `Code.gs` ต้อง Deploy เวอร์ชันใหม่
- **Export:** ปุ่ม Export Excel (หลายแท็บ: สรุป, Top Item, ร้านค้า, SKU ควรเติม, ข้อมูลดิบ ตามตัวกรองปัจจุบัน; ใช้ไลบรารี SheetJS จาก cdnjs ถ้าโหลดไม่ได้จะส่งออก CSV แทน) และ Export PDF (เปิดหน้าต่างพิมพ์ของเบราว์เซอร์ เลือก "บันทึกเป็น PDF")

## ข้อควรระวัง

- Web app แบบ Anyone หมายความว่าใครที่ได้ URL ก็ดึงข้อมูลชีตได้ และ URL กับ `API_KEY` มองเห็นได้ใน `index.html` ถ้าข้อมูลร้านค้าไม่ควรเปิดเผย ให้ใช้ repo แบบ private ร่วมกับ GitHub Pages ที่จำกัดผู้เข้าชม หรือใช้โหมดอัปโหลด CSV (ปล่อย `API_URL` ว่างแล้วลากไฟล์ CSV มาวาง)
- ถ้าโหลดจาก Apps Script ไม่ได้ เว็บจะแสดงข้อความ error และให้เลือกไฟล์ CSV แทน
- "มี Stock" = Status เป็น InfoOnly, Sufficient, Insufficient / "ไม่มี Stock" = NotFound
