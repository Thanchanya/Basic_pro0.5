# Handoff: Interactive Dashboard — การวิเคราะห์อุบัติเหตุทางถนนในประเทศไทย

**Project:** Thailand Road Accident Analysis  
**Prepared:** 2026-10-06  
**Purpose:** ส่งต่อให้ AI/Developer พัฒนา Interactive Dashboard ด้วย Python โดยใช้เฉพาะ Open Data จากหน่วยงานรัฐที่ตรวจสอบแหล่งที่มาได้

---

## 1. Executive decision — ชุดข้อมูลที่ควรใช้

### Primary dataset (ต้องใช้)
**อุบัติเหตุบนโครงข่ายถนนของกระทรวงคมนาคม (TRAMS)** — ใช้เป็น fact table หลักของ Dashboard

เหตุผล: เป็นข้อมูลระดับ “เหตุการณ์” (event-level) และมีตัวแปรที่ตอบโจทย์ Dashboard เกือบทั้งหมดในชุดเดียว ได้แก่ วัน/เวลา จังหวัด ถนน/กิโลเมตร พิกัด ประเภทยานพาหนะ บริเวณเกิดเหตุ มูลเหตุสันนิษฐาน ลักษณะการชน สภาพอากาศ จำนวนรถ ผู้เสียชีวิต ผู้บาดเจ็บสาหัส และผู้บาดเจ็บเล็กน้อย

### Secondary dataset (แนะนำให้ใช้)
**ข้อมูลผู้เสียชีวิตจากอุบัติเหตุทางถนน — ระบบบูรณาการข้อมูลการตายจากอุบัติเหตุทางถนน (3 ฐาน), กรมควบคุมโรค**

ใช้เป็น mortality reference/validation และใช้ทำหน้าเสริมเกี่ยวกับผู้เสียชีวิต เช่น อายุ เพศ หมวกนิรภัย เข็มขัดนิรภัย วันเวลาและจังหวัดเกิดเหตุ **ไม่ควร row-level join กับ TRAMS โดยสมมติว่าเป็นเหตุเดียวกัน** เพราะไม่มี shared accident ID ที่ยืนยันตรงกัน

### Optional datasets (ไม่จำเป็นสำหรับ MVP)
- ข้อมูลอุบัติเหตุบนทางพิเศษ — การทางพิเศษแห่งประเทศไทย: ใช้เมื่ออยากทำ drill-down เฉพาะทางพิเศษ
- ข้อมูลอุบัติเหตุทางถนน e-Report ระดับจังหวัด: ใช้เฉพาะกรณีต้องการวิเคราะห์พื้นที่ที่ชุดนั้นครอบคลุม ไม่ควรนำมาแทน nationwide TRAMS

---

# 2. Dataset A — PRIMARY

## ชื่อชุดข้อมูล
อุบัติเหตุบนโครงข่ายถนนของกระทรวงคมนาคม

## หน่วยงาน/แหล่งที่มา
ศูนย์เทคโนโลยีสารสนเทศและการสื่อสาร สำนักงานปลัดกระทรวงคมนาคม  
ต้นทาง: ระบบ TRansport Accident Management Systems (TRAMS)

## ขอบเขต
อุบัติเหตุบนถนนทางหลวง ทางหลวงชนบท และทางด่วนในโครงข่ายของกระทรวงคมนาคม

## ช่วงเวลาข้อมูลที่พบใน Catalog
มี resource แยกปี **พ.ศ. 2562–2569 (ค.ศ. 2019–2026)** โดยปี 2569 เป็นข้อมูลปีปัจจุบันและอาจยังไม่ครบปี

## รูปแบบ
CSV และ XLS/XLSX; Open Data Common; ไม่มีการจำกัดการเข้าถึง

## หน้าแหล่งข้อมูล
https://datagov.mot.go.th/th/dataset/roadaccident

## Direct download ที่ตรวจพบ
- 2024 / พ.ศ. 2567 CSV: https://datagov.mot.go.th/dataset/7e077ffd-dc4f-4dc6-a71c-0813726f3c12/resource/83e9fae0-5f33-45e7-9a6d-6d5ad2060a08/download/accident2024.csv
- 2026 / พ.ศ. 2569 CSV: https://datagov.mot.go.th/dataset/7e077ffd-dc4f-4dc6-a71c-0813726f3c12/resource/4625b7aa-99a4-4dfb-9a0f-6402814d0f2c/download/accident2026.csv

> หมายเหตุ: resource page และ URL ดาวน์โหลดถูกตรวจพบจาก Catalog แต่ environment ที่จัดทำ handoff นี้ไม่สามารถรับ binary/CSV จาก MOT server ได้สำเร็จ จึงไม่ได้แนบไฟล์ที่อ้างว่าเป็นข้อมูลจริงโดยไม่ตรวจสอบ ให้ developer ดาวน์โหลดจาก URL ต้นทางบนเครื่องที่พัฒนา

## Data Dictionary ที่ยืนยันจาก resource ปี 2567
| Variable | ความหมาย / การใช้ |
|---|---|
| ปีที่เกิดเหตุ | ปี ค.ศ. ของเหตุการณ์ |
| วันที่เกิดเหตุ | วันที่เกิดอุบัติเหตุ |
| เวลา | เวลาเกิดเหตุ |
| วันที่รายงาน | วันที่บันทึก/รายงานเหตุ |
| เวลาที่รายงาน | เวลารายงาน |
| ACC_CODE | รหัสอุบัติเหตุใน TRAMS — primary event identifier ที่ควรรักษาไว้ |
| หน่วยงาน | หน่วยงานผู้รายงาน เช่น กรมทางหลวง |
| สายทางหน่วยงาน | ประเภท/หน่วยงานสายทาง |
| รหัสสายทาง | หมายเลขสายทาง |
| สายทาง | ชื่อสายทาง |
| KM | กม. ที่เกิดเหตุ |
| จังหวัด | จังหวัดที่เกิดเหตุ |
| รถคันที่1 | ประเภทยานพาหนะคันแรก |
| บริเวณที่เกิดเหตุ | ลักษณะบริเวณ/ลักษณะทาง |
| มูลเหตุสันนิษฐาน | สาเหตุ/ปัจจัยที่สันนิษฐาน เช่น ขับรถเร็วเกินอัตรากำหนด |
| ลักษณะการเกิดเหตุ | รูปแบบการชน เช่น ชนท้าย |
| สภาพอากาศ | สภาพอากาศขณะเกิดเหตุ |
| LATITUDE | ละติจูดจุดเกิดเหตุ |
| LONGITUDE | ลองจิจูดจุดเกิดเหตุ |
| รถที่เกิดเหตุ | จำนวนรถทั้งหมดในเหตุการณ์ |
| รถและคนที่เกิดเหตุ | จำนวนรถและคนเดินเท้าที่เกี่ยวข้อง |
| รถจักรยานยนต์ | จำนวนรถจักรยานยนต์ |
| รถสามล้อเครื่อง | จำนวนรถสามล้อเครื่อง |
| รถยนต์นั่งส่วนบุคคล | จำนวนรถยนต์นั่งส่วนบุคคล/สาธารณะ |
| รถตู้ | จำนวนรถตู้ |
| รถปิคอัพโดยสาร | จำนวนรถปิคอัพโดยสาร |
| รถโดยสารมากกว่า4ล้อ | จำนวนรถโดยสาร >4 ล้อ |
| รถปิคอัพบรรทุก4ล้อ | จำนวนรถปิคอัพบรรทุก 4 ล้อ |
| รถบรรทุก6ล้อ | จำนวนรถบรรทุก 6 ล้อ |
| รถบรรทุกไม่เกิน10ล้อ | จำนวนรถบรรทุก >6 ถึง 10 ล้อ |
| รถบรรทุกมากกว่า10ล้อ | จำนวนรถบรรทุก >10 ล้อ/รถพ่วง |
| รถอีแต๋น | จำนวนรถอีแต๋น |
| รถอื่นๆ | จำนวนรถประเภทอื่น |
| คนเดินเท้า | จำนวนคนเดินเท้าที่เกี่ยวข้อง |
| ผู้เสียชีวิต | จำนวนผู้เสียชีวิตในเหตุการณ์ |
| ผู้บาดเจ็บสาหัส | จำนวนผู้บาดเจ็บสาหัส |
| ผู้บาดเจ็บเล็กน้อย | จำนวนผู้บาดเจ็บเล็กน้อย |
| รวมจำนวนผู้บาดเจ็บ | ผู้บาดเจ็บทั้งหมด |

## วิเคราะห์อะไรได้
- KPI: จำนวนอุบัติเหตุ, ผู้เสียชีวิต, ผู้บาดเจ็บ, fatal accidents, severe injury accidents
- Trend รายปี/เดือน/วันในสัปดาห์/ชั่วโมง
- จังหวัดที่มีเหตุสูงสุดและความรุนแรงสูงสุด
- Heatmap วัน × ชั่วโมง
- สาเหตุหลักของอุบัติเหตุ และสาเหตุที่สัมพันธ์กับความรุนแรง
- ยานพาหนะที่เกี่ยวข้องมากที่สุด
- รูปแบบการชน เช่น ชนท้าย/พลิกคว่ำ ฯลฯ
- สภาพอากาศและบริเวณถนนที่สัมพันธ์กับเหตุ
- แผนที่จุดเกิดเหตุจาก LATITUDE/LONGITUDE
- วิเคราะห์สายทาง/กิโลเมตร เพื่อหา hotspot บนโครงข่าย

## ข้อจำกัด
1. ครอบคลุม “โครงข่ายถนนของกระทรวงคมนาคม” ไม่ควรตีความว่าเป็น census ของอุบัติเหตุทุกถนนทุกประเภทในประเทศไทย
2. `มูลเหตุสันนิษฐาน` เป็นสาเหตุที่รายงาน/สันนิษฐาน ไม่ควรอธิบายเป็น causal proof
3. ปีล่าสุดอาจเป็น year-to-date
4. schema/category spelling อาจเปลี่ยนข้ามปี ต้อง normalize ก่อน concat
5. missing/invalid coordinates ต้องตรวจสอบก่อนทำแผนที่
6. จำนวนเหตุให้ใช้ `ACC_CODE.nunique()` หลังรวมหลายปี หากพบ duplicate ไม่ควรใช้จำนวนแถวโดยไม่ตรวจ

---

# 3. Dataset B — SECONDARY / MORTALITY REFERENCE

## ชื่อชุดข้อมูล
ข้อมูลผู้เสียชีวิตจากอุบัติเหตุทางถนน จากระบบบูรณาการข้อมูลการตายจากอุบัติเหตุทางถนน (3 ฐาน)

## หน่วยงาน
กรมควบคุมโรค กระทรวงสาธารณสุข — กองป้องกันการบาดเจ็บ

## ความสำคัญ
หน้า dataset ระบุว่าเป็นข้อมูลที่ใช้เป็น **ฐานอ้างอิงจำนวนผู้เสียชีวิตจากอุบัติเหตุทางถนนของประเทศไทย** โดยบูรณาการข้อมูล 3 ฐาน (death certificate, e-claim, police)

## ช่วงเวลา
มี resource รายปี **พ.ศ. 2554–2569 (ค.ศ. 2011–2026)** ในหน้า dataset; ปีล่าสุดอาจยังไม่ครบปี

## รูปแบบ/License
ข้อมูลระเบียนระดับบุคคล; CSV/Database; Creative Commons Attribution; ข้อมูลสาธารณะ

## หน้าแหล่งข้อมูล
https://data.go.th/dataset/rtddi

## ตัวแปรสำคัญที่ยืนยันจาก Data Dictionary
- `id`
- `DEAD_YEAR(Budha)` — ปีเสียชีวิต พ.ศ.
- `DEAD_YEAR` — ปีเสียชีวิต
- `Age` — อายุ
- `Sex` — เพศ
- `BirthYear`
- `NationalityId`
- `Tumbol`, `District`, `Province` — ที่อยู่
- `RiskHelmet` — หมวกนิรภัย
- `RiskSafetyBelt` — เข็มขัดนิรภัย
- `DeadDate` — วันที่เสียชีวิต
- `DateRec` — วันที่เกิดเหตุจาก E-claim
- `TimeRec` — เวลาเกิดเหตุ
- `AccSubDist`, `AccDist`, `AccProv` — ตำบล/อำเภอ/จังหวัดที่เกิดเหตุ
- `AccLat`, `AccLong` — พิกัดเกิดเหตุ

> Data Dictionary มีมากกว่ารายการด้านบน ให้โหลด Data Dictionary ต้นทางก่อนเขียน ETL เพื่อยืนยันทุกคอลัมน์ใน resource ปีที่ใช้จริง

## วิเคราะห์อะไรได้
- จำนวนผู้เสียชีวิตอ้างอิงระดับประเทศ/จังหวัด/ปี
- อายุและเพศของผู้เสียชีวิต
- temporal pattern ของ fatal road crashes
- จังหวัดเกิดเหตุของผู้เสียชีวิต
- helmet / seatbelt risk dimensions (ตามความสมบูรณ์ของข้อมูล)
- เปรียบเทียบแนวโน้ม mortality กับจำนวนผู้เสียชีวิตที่รายงานใน TRAMS ในระดับ aggregate

## ข้อจำกัด
- หน่วยข้อมูลเป็น “บุคคลผู้เสียชีวิต” ไม่ใช่ “เหตุการณ์อุบัติเหตุ”
- ห้าม sum จำนวนแถวรวมกับ `ผู้เสียชีวิต` ใน TRAMS เพราะจะ double count คนละระบบ
- ไม่มี `ACC_CODE` ของ TRAMS ที่ยืนยันว่าเชื่อมเหตุการณ์เดียวกันได้
- ควรใช้เป็น separate fact table และเชื่อมผ่าน shared dimensions เช่น ปี/เดือน/จังหวัด สำหรับการเปรียบเทียบ aggregate เท่านั้น

---

# 4. Dataset C — OPTIONAL

## ชื่อชุดข้อมูล
ข้อมูลอุบัติเหตุบนทางพิเศษ

## หน่วยงาน
การทางพิเศษแห่งประเทศไทย (EXAT)

## หน้าแหล่งข้อมูล
https://datagov.mot.go.th/th/dataset/exat-accident

## รูปแบบ
CSV และ JSON/API

## ข้อมูลที่ระบุใน Catalog
อุบัติเหตุบนทางพิเศษ, อุบัติเหตุร้ายแรง, รถชน, ผู้บาดเจ็บ, ผู้เสียชีวิต; พบ resources ตั้งแต่ปีงบประมาณ 2559 และมี API document เชื่อมจากหน้า dataset

## ใช้เมื่อ
ต้องการหน้าเฉพาะ Expressway หรือ cross-check รายละเอียดทางพิเศษ

## ข้อจำกัด
TRAMS ระบุว่าครอบคลุมทางด่วนอยู่แล้ว จึงไม่ควร append EXAT เข้ากับ TRAMS โดยตรงก่อนทำ deduplication เพราะเสี่ยงนับเหตุซ้ำ

---

# 5. Dataset ที่ค้นพบแต่ไม่เลือกเป็นข้อมูลหลัก

## e-Report / ชุดข้อมูลจังหวัด
ตัวอย่าง: “อุบัติเหตุทางถนน” ของสำนักงานจังหวัดสุพรรณบุรี
- หน้า: https://data.go.th/dataset/dpm020
- source: ระบบรายงานข้อมูลอุบัติเหตุทางถนน (e-Report)
- CSV, Open Data Common
- ขอบเขตระดับอำเภอ
- ปีข้อมูล metadata ระบุช่วงประมาณ 2566–2569

**ไม่เลือกเป็น national primary:** เป็นชุดที่เผยแพร่โดยจังหวัดและ coverage ไม่ใช่ nationwide event table เดียวกัน

## ชุดสถิติรายจังหวัดอื่น
พบชุด “ผู้บาดเจ็บจากอุบัติเหตุทางถนน”, “อุบัติเหตุการจราจรทางบก”, “สาเหตุการเกิดอุบัติเหตุทางถนน” ฯลฯ แต่หลายชุดเป็นจังหวัดเฉพาะ จึงไม่ควรนำมาใช้สร้างภาพรวมประเทศไทยโดยตรง

---

# 6. Recommended files / project structure

```text
project/
├─ data/
│  ├─ raw/
│  │  ├─ trams/
│  │  │  ├─ accident2019.csv
│  │  │  ├─ accident2020.csv
│  │  │  ├─ ...
│  │  │  └─ accident2026.csv
│  │  └─ rtddi/
│  │     ├─ rtddi_2019.csv
│  │     ├─ ...
│  │     └─ rtddi_2026.csv
│  └─ processed/
│     ├─ accidents_trams_clean.csv        # MAIN DASHBOARD FACT
│     ├─ mortality_3base_clean.csv        # mortality reference fact
│     └─ province_year_validation.csv      # aggregate comparison table
├─ src/
│  ├─ download_data.py
│  ├─ clean_trams.py
│  ├─ clean_rtddi.py
│  └─ build_metrics.py
├─ app.py
└─ ROAD_ACCIDENT_DASHBOARD_HANDOFF.md
```

**Main file for Dashboard:** `data/processed/accidents_trams_clean.csv`

---

# 7. Data integration / Join strategy

## 7.1 TRAMS across years
Use **UNION/CONCAT**, not JOIN.

1. Download annual files.
2. Standardize column names.
3. Add `source_year` and `source_file`.
4. Parse date/time.
5. Normalize province/category spelling.
6. Concat all years.
7. Check duplicate `ACC_CODE` across files.

Recommended event key: `ACC_CODE`.

## 7.2 TRAMS ↔ 3-base mortality
**Do not direct row-level join.**

Recommended method:
- Aggregate TRAMS by `year + province` (optionally month)
- Aggregate 3-base by `year + AccProv`
- Join the aggregate tables on standardized `year + province`
- Label metrics explicitly:
  - `trams_deaths`
  - `three_base_deaths`
  - `death_difference`
  - `death_ratio_trams_to_3base`

This comparison communicates differences in system coverage rather than pretending the sources measure identical universes.

## 7.3 Province dimension
Create canonical province mapping:
- `province_key`
- `province_th`
- optional `province_en`
- optional `region`

Use this mapping to standardize `จังหวัด` (TRAMS) and `AccProv` (3-base).

---

# 8. Derived variables recommended for Dashboard

From TRAMS create:
- `accident_date`
- `year`, `month`, `month_name`, `quarter`
- `day_of_week`
- `hour`
- `time_band` เช่น 00–05, 06–09, 10–15, 16–19, 20–23
- `is_weekend`
- `injuries_total`
- `casualties_total = deaths + injuries_total`
- `has_death = deaths > 0`
- `has_serious_injury = serious_injuries > 0`
- `severity_score` (ต้องประกาศสูตรชัด เช่น 3*deaths + 2*serious + 1*minor; ใช้เพื่อ visualization ไม่ใช่มาตรฐานราชการ)
- `vehicle_group_primary`
- `cause_group`
- `road_environment_group`
- `valid_coordinate`

Do not overwrite raw categories; keep raw + normalized columns.

---

# 9. Dashboard questions / visuals

Dashboard ควรตอบคำถามหลักต่อไปนี้:

1. สถานการณ์โดยรวมเป็นอย่างไร? — KPI cards: accidents, deaths, injuries, fatality per 100 accidents
2. เกิดมากเมื่อไร? — yearly/monthly trend + day-hour heatmap
3. เกิดที่ไหน? — province ranking + point map/hotspot map
4. อะไรเป็นปัจจัยสำคัญ? — cause ranking, weather, road environment
5. ยานพาหนะอะไรเกี่ยวข้องมาก? — vehicle distribution / stacked chart
6. เหตุแบบไหนรุนแรง? — collision type × deaths/serious injuries
7. จุด/สายทางเสี่ยงอยู่ตรงไหน? — route/KM hotspot
8. ตัวเลขผู้เสียชีวิตจาก TRAMS ต่างจาก 3-base อย่างไร? — separate validation/comparison view พร้อมคำเตือน coverage

Recommended global filters: year, province, agency/road network, cause, vehicle, collision type, weather, severity.

---

# 10. Data quality checks before publishing

- Assert `ACC_CODE` uniqueness within expected grain
- Compare row count vs unique ACC_CODE
- Check null rate by year for key fields
- Check `ผู้เสียชีวิต`, `รวมจำนวนผู้บาดเจ็บ` >= 0
- Recalculate injury total from serious + minor and compare with reported total
- Validate latitude roughly 5–21 and longitude roughly 97–106 for Thailand; flag, do not silently delete
- Check impossible dates/times
- Normalize Thai whitespace and category spelling
- Show latest available date in Dashboard so users know whether 2026 is partial
- Never label TRAMS counts as “อุบัติเหตุทั้งหมดของประเทศไทย” without a coverage note

---

# 11. Python loading template

```python
import pandas as pd

TRAMS_2024 = "https://datagov.mot.go.th/dataset/7e077ffd-dc4f-4dc6-a71c-0813726f3c12/resource/83e9fae0-5f33-45e7-9a6d-6d5ad2060a08/download/accident2024.csv"

# If direct pandas read is blocked by server/network, download with browser/requests first.
df = pd.read_csv(TRAMS_2024, encoding="utf-8-sig")

print(df.shape)
print(df.columns.tolist())
print(df.head())
```

For annual ingestion, keep the Catalog page as the source of truth for current resource URLs rather than guessing filenames.

---

# 12. Handoff Description for next AI

You are continuing a project titled **“การวิเคราะห์อุบัติเหตุทางถนนในประเทศไทย”**. Build an Interactive Dashboard from official Thai Open Data.

**Source of truth / main fact:** TRAMS — “อุบัติเหตุบนโครงข่ายถนนของกระทรวงคมนาคม”. Use annual CSV files from 2019–2026 where schema is compatible. The processed main file must be named `accidents_trams_clean.csv`, one row per TRAMS accident event, retaining `ACC_CODE`.

TRAMS contains event date/time, reporting date/time, reporting agency, road network/route/KM, province, first vehicle, road environment, suspected cause, collision type, weather, latitude/longitude, vehicle counts by type, pedestrian count, deaths, serious injuries, minor injuries and total injuries.

Use the DDC **3-base mortality** dataset as a **separate mortality fact/reference**, not as a replacement for TRAMS and not as a direct event-level merge. It contains individual death records and dimensions such as age, sex, helmet/seatbelt indicators, accident date/time, accident province/district and coordinates. Aggregate both sources to compatible geography/time before comparison.

Do not append provincial e-Report datasets to the national TRAMS table unless coverage, grain and duplicate handling have been explicitly validated.

Build derived time/severity/category fields, run data-quality checks, and make all Dashboard metrics filter-responsive. Include a visible data-source/coverage note stating that TRAMS represents accidents on Ministry of Transport road networks and therefore must not automatically be interpreted as every road accident in Thailand.

Priority views: overview KPIs, time trends, province map/ranking, day-hour heatmap, suspected causes, vehicle types, collision types, road/weather conditions, route hotspots, severity, and an optional TRAMS-vs-3-base mortality comparison.

---

# 13. Official source links

1. MOT Data Catalog — TRAMS road accidents  
   https://datagov.mot.go.th/th/dataset/roadaccident

2. TRAMS system  
   https://trams.mot.go.th/

3. TRAMS 2024 CSV direct resource  
   https://datagov.mot.go.th/dataset/7e077ffd-dc4f-4dc6-a71c-0813726f3c12/resource/83e9fae0-5f33-45e7-9a6d-6d5ad2060a08/download/accident2024.csv

4. TRAMS 2026 CSV direct resource  
   https://datagov.mot.go.th/dataset/7e077ffd-dc4f-4dc6-a71c-0813726f3c12/resource/4625b7aa-99a4-4dfb-9a0f-6402814d0f2c/download/accident2026.csv

5. DDC / data.go.th — 3-base road traffic death integration  
   https://data.go.th/dataset/rtddi

6. EXAT accident data (optional)  
   https://datagov.mot.go.th/th/dataset/exat-accident

7. Provincial e-Report example — Suphan Buri (not national primary)  
   https://data.go.th/dataset/dpm020

---

# 14. Source verification notes

Verified on 2026-10-06 from official government catalogs:
- MOT catalog lists TRAMS annual resources from 2562 through 2569, CSV/XLSX, Open Data Common, public access, event-level grain.
- 2024 TRAMS resource exposes a Data Dictionary confirming the variables listed in this handoff.
- DDC/data.go.th lists 3-base annual resources from 2554 through 2569 and describes it as Thailand's reference road-traffic mortality integration dataset.
- EXAT catalog exposes CSV and JSON/API resources.

When development starts, re-check the catalog pages for updated resource URLs and the latest complete date before refreshing the Dashboard.
