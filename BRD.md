# BRD --- Interactive Dashboard: การวิเคราะห์อุบัติเหตุทางถนนในประเทศไทย

**Project:** Thailand Road Accident Analysis\
**Document Type:** Business Requirements Document (BRD)\
**Primary Technology:** Python + Plotly\
**Primary Data Source:** TRAMS --- อุบัติเหตุบนโครงข่ายถนนของกระทรวงคมนาคม\
**Purpose:** เอกสารส่งต่อให้ Antigravity/Developer พัฒนา Interactive
Dashboard โดยใช้เฉพาะข้อมูลที่มีอยู่จริงใน Open Data

------------------------------------------------------------------------

## 1. Business Objective

พัฒนา Interactive Dashboard
เพื่อวิเคราะห์สถานการณ์อุบัติเหตุทางถนนบนโครงข่ายถนนของกระทรวงคมนาคมในประเทศไทย
โดยช่วยให้ผู้ใช้มองเห็น 4 ประเด็นหลักอย่างชัดเจน ได้แก่

1.  **พื้นที่เสี่ยง** --- จังหวัด/พื้นที่/สายทางที่เกิดอุบัติเหตุหรือมีความรุนแรงสูง
2.  **ช่วงเวลาเสี่ยง** --- ปี เดือน วัน และช่วงเวลาที่เกิดอุบัติเหตุสูง
3.  **ปัจจัยเสี่ยง** --- มูลเหตุสันนิษฐาน ประเภทยานพาหนะ ลักษณะการชน สภาพอากาศ
    และบริเวณที่เกิดเหตุ
4.  **ความรุนแรง** --- จำนวนผู้เสียชีวิต ผู้บาดเจ็บสาหัส ผู้บาดเจ็บเล็กน้อย
    และผู้บาดเจ็บรวม

Dashboard ต้องรองรับการสำรวจข้อมูลแบบ Interactive ผ่าน Global Filters และทำให้
KPI/กราฟที่เกี่ยวข้องอัปเดตพร้อมกันตามข้อมูลที่ผู้ใช้เลือก

> **Coverage note:** TRAMS ครอบคลุมอุบัติเหตุบนโครงข่ายถนนของกระทรวงคมนาคม เช่น
> ทางหลวง ทางหลวงชนบท และทางด่วน จึงห้ามนำเสนอว่าเป็น census ของ
> "อุบัติเหตุทุกเหตุบนถนนทุกประเภทในประเทศไทย" โดยไม่มีคำอธิบายขอบเขตข้อมูล

------------------------------------------------------------------------

## 2. Key Questions

Dashboard ต้องช่วยตอบคำถามหลักดังนี้

### Q1 --- Where / When / How severe?

อุบัติเหตุเกิดขึ้นที่ไหน เมื่อใด และมีความรุนแรงมากน้อยเพียงใด?

### Q2 --- What factors are associated?

มูลเหตุสันนิษฐาน ประเภทยานพาหนะ ลักษณะการชน สภาพอากาศ และบริเวณที่เกิดเหตุ
มีรูปแบบหรือความสัมพันธ์เชิงสถิติกับความรุนแรงอย่างไร?

> ห้ามตีความ association เป็น causal relationship เนื่องจากข้อมูล observational
> และ `มูลเหตุสันนิษฐาน` เป็นสาเหตุที่บันทึก/สันนิษฐานจากระบบ

### Q3 --- Where/when should monitoring be prioritized?

จังหวัด พื้นที่ สายทาง วัน หรือช่วงเวลาใดมีจำนวนเหตุหรือความรุนแรงสูง
และควรได้รับความสำคัญในการเฝ้าระวัง?

------------------------------------------------------------------------

## 3. Dataset ที่ใช้

### 3.1 Primary Dataset --- Required

**ชื่อ:** อุบัติเหตุบนโครงข่ายถนนของกระทรวงคมนาคม\
**ระบบต้นทาง:** TRansport Accident Management Systems (TRAMS)\
**หน่วยงาน:** ศูนย์เทคโนโลยีสารสนเทศและการสื่อสาร สำนักงานปลัดกระทรวงคมนาคม\
**ระดับข้อมูล:** Event-level --- หนึ่งเหตุการณ์อุบัติเหตุระบุด้วย `ACC_CODE`\
**ช่วงปีที่ Catalog พบ:** พ.ศ. 2562--2569 / ค.ศ. 2019--2026\
**ไฟล์ที่ควรใช้ใน Dashboard:** `data/processed/accidents_trams_clean.csv`

หน้าแหล่งข้อมูล:\
https://datagov.mot.go.th/th/dataset/roadaccident

ตัวอย่าง Direct Download ที่ตรวจพบ: - 2024:
https://datagov.mot.go.th/dataset/7e077ffd-dc4f-4dc6-a71c-0813726f3c12/resource/83e9fae0-5f33-45e7-9a6d-6d5ad2060a08/download/accident2024.csv -
2026:
https://datagov.mot.go.th/dataset/7e077ffd-dc4f-4dc6-a71c-0813726f3c12/resource/4625b7aa-99a4-4dfb-9a0f-6402814d0f2c/download/accident2026.csv

### 3.2 Secondary Dataset --- Optional / Validation

**ชื่อ:** ข้อมูลผู้เสียชีวิตจากอุบัติเหตุทางถนน
จากระบบบูรณาการข้อมูลการตายจากอุบัติเหตุทางถนน (3 ฐาน)\
**หน่วยงาน:** กรมควบคุมโรค กระทรวงสาธารณสุข\
**ระดับข้อมูล:** Person-level mortality record\
**ช่วงปีที่ Catalog พบ:** พ.ศ. 2554--2569\
**การใช้:** ใช้เป็น mortality reference/validation หรือหน้าเปรียบเทียบ
aggregate เท่านั้น

https://data.go.th/dataset/rtddi

**ห้าม direct row-level join กับ TRAMS** เพราะไม่มี shared accident ID
ที่ยืนยันว่าเป็นเหตุการณ์เดียวกัน

------------------------------------------------------------------------

## 4. Data Dictionary --- Primary TRAMS Fields

ตารางนี้ระบุเฉพาะตัวแปรที่ยืนยันจาก Data Dictionary ของ TRAMS resource ปี 2567 ใน
Handoff ก่อนหน้า

  --------------------------------------------------------------------------
  Field                   ความหมาย                   การใช้ใน Dashboard
  ----------------------- -------------------------- -----------------------
  `ปีที่เกิดเหตุ`              ปี ค.ศ. ที่เกิดเหตุ             Filter, trend

  `วันที่เกิดเหตุ`             วันที่เกิดอุบัติเหตุ               Date analysis

  `เวลา`                  เวลาเกิดเหตุ                 Hour/time-band analysis

  `วันที่รายงาน`             วันที่รายงานเหตุ               Data quality / optional

  `เวลาที่รายงาน`           เวลารายงานเหตุ              Data quality / optional

  `ACC_CODE`              รหัสอุบัติเหตุ                  Event identifier
                                                     และใช้คำนวณจำนวนเหตุ

  `หน่วยงาน`               หน่วยงานผู้รายงาน             Filter / coverage

  `สายทางหน่วยงาน`         ประเภท/หน่วยงานสายทาง       Road-network analysis

  `รหัสสายทาง`             หมายเลขสายทาง              Route analysis

  `สายทาง`                ชื่อสายทาง                   Route hotspot

  `KM`                    กม. ที่เกิดเหตุ                Route/KM hotspot

  `จังหวัด`                 จังหวัดที่เกิดเหตุ               Province
                                                     filter/map/ranking

  `รถคันที่1`                ประเภทยานพาหนะคันแรก        Vehicle analysis

  `บริเวณที่เกิดเหตุ`          ลักษณะบริเวณ/ลักษณะทาง        Road environment
                                                     analysis

  `มูลเหตุสันนิษฐาน`          สาเหตุ/ปัจจัยที่สันนิษฐาน         Cause analysis

  `ลักษณะการเกิดเหตุ`        รูปแบบการชน                 Collision analysis

  `สภาพอากาศ`             สภาพอากาศขณะเกิดเหตุ         Weather analysis

  `LATITUDE`              ละติจูด                      Point/hotspot map

  `LONGITUDE`             ลองจิจูด                     Point/hotspot map

  `รถที่เกิดเหตุ`             จำนวนรถในเหตุการณ์           Event context

  `รถและคนที่เกิดเหตุ`        จำนวนรถ/คนเดินเท้าที่เกี่ยวข้อง   Event context

  `รถจักรยานยนต์`           จำนวนรถจักรยานยนต์           Vehicle composition

  `รถสามล้อเครื่อง`          จำนวนรถสามล้อเครื่อง          Vehicle composition

  `รถยนต์นั่งส่วนบุคคล`        จำนวนรถยนต์นั่ง               Vehicle composition

  `รถตู้`                   จำนวนรถตู้                   Vehicle composition

  `รถปิคอัพโดยสาร`          จำนวนรถปิคอัพโดยสาร          Vehicle composition

  `รถโดยสารมากกว่า4ล้อ`     จำนวนรถโดยสาร \>4 ล้อ       Vehicle composition

  `รถปิคอัพบรรทุก4ล้อ`        จำนวนรถปิคอัพบรรทุก           Vehicle composition

  `รถบรรทุก6ล้อ`            จำนวนรถบรรทุก 6 ล้อ          Vehicle composition

  `รถบรรทุกไม่เกิน10ล้อ`      จำนวนรถบรรทุก \>6 ถึง 10 ล้อ  Vehicle composition

  `รถบรรทุกมากกว่า10ล้อ`     จำนวนรถบรรทุก \>10 ล้อ/รถพ่วง Vehicle composition

  `รถอีแต๋น`                จำนวนรถอีแต๋น                Vehicle composition

  `รถอื่นๆ`                 จำนวนรถประเภทอื่น            Vehicle composition

  `คนเดินเท้า`              จำนวนคนเดินเท้าที่เกี่ยวข้อง      Vulnerable road-user
                                                     analysis

  `ผู้เสียชีวิต`               จำนวนผู้เสียชีวิตในเหตุ          KPI/severity

  `ผู้บาดเจ็บสาหัส`           จำนวนผู้บาดเจ็บสาหัส           KPI/severity

  `ผู้บาดเจ็บเล็กน้อย`         จำนวนผู้บาดเจ็บเล็กน้อย         KPI/severity

  `รวมจำนวนผู้บาดเจ็บ`       ผู้บาดเจ็บทั้งหมด               KPI/severity
  --------------------------------------------------------------------------

### Derived Fields ที่อนุญาต

Derived fields ต่อไปนี้สร้างได้จากข้อมูลจริง โดยต้องเก็บ raw fields เดิมไว้:

-   `accident_date` = parse `วันที่เกิดเหตุ`
-   `year` = year of accident
-   `month` / `month_name`
-   `day_of_week`
-   `hour` = hour from `เวลา`
-   `time_band` = กลุ่มช่วงเวลาที่กำหนดจาก hour
-   `is_weekend`
-   `injuries_total` = ใช้ `รวมจำนวนผู้บาดเจ็บ`; หากต้อง recalculate
    ให้ตรวจเทียบ `ผู้บาดเจ็บสาหัส + ผู้บาดเจ็บเล็กน้อย`
-   `casualties_total = ผู้เสียชีวิต + รวมจำนวนผู้บาดเจ็บ`
-   `has_death = ผู้เสียชีวิต > 0`
-   `has_serious_injury = ผู้บาดเจ็บสาหัส > 0`
-   `valid_coordinate` = flag จากการตรวจพิกัด
-   `province_key` / `region` = เพิ่มได้จาก canonical province mapping
    ที่ตรวจสอบแหล่งที่มาแล้ว

**ไม่ใช้ `severity_score` เป็น KPI หลักใน BRD นี้** เพราะไม่มีมาตรฐาน severity
score ดังกล่าวอยู่ใน Dataset ต้นทาง หากต้องการสร้างในอนาคต ต้องระบุว่าเป็น
analytical index ที่ผู้พัฒนากำหนดเองอย่างชัดเจน

------------------------------------------------------------------------

## 5. KPI Definitions

KPI ทุกตัวต้องคำนวณหลัง Apply Global Filters แล้ว

### KPI 1 --- จำนวนอุบัติเหตุ

**Definition:** จำนวนเหตุการณ์อุบัติเหตุที่ไม่ซ้ำ\
**Formula:** `nunique(ACC_CODE)`\
**Display:** Integer

> ห้ามใช้ `len(df)` โดยไม่ตรวจ duplicate `ACC_CODE`

### KPI 2 --- จำนวนผู้เสียชีวิต

**Definition:** จำนวนผู้เสียชีวิตรวมในเหตุการณ์ที่ถูกเลือก\
**Formula:** `SUM(ผู้เสียชีวิต)`

### KPI 3 --- จำนวนผู้บาดเจ็บ

**Definition:** จำนวนผู้บาดเจ็บรวม\
**Formula:** `SUM(รวมจำนวนผู้บาดเจ็บ)`

### KPI 4 --- จำนวนผู้บาดเจ็บสาหัส

**Definition:** จำนวนผู้บาดเจ็บสาหัสรวม\
**Formula:** `SUM(ผู้บาดเจ็บสาหัส)`\
**Status:** Recommended supplementary KPI

### KPI 5 --- เหตุที่มีผู้เสียชีวิต

**Definition:** จำนวนเหตุการณ์ที่ `ผู้เสียชีวิต > 0`\
**Formula:** `nunique(ACC_CODE where ผู้เสียชีวิต > 0)`

### KPI 6 --- ผู้เสียชีวิตต่อ 100 อุบัติเหตุ

**Definition:** ตัวชี้วัดความรุนแรงเชิงเปรียบเทียบ\
**Formula:** `(SUM(ผู้เสียชีวิต) / nunique(ACC_CODE)) * 100`\
**Display:** Rate per 100 accidents

> ตัวชี้วัดนี้ไม่ใช่ population risk และไม่ควรเรียกว่าอัตราตายของประชากร

### KPI 7 --- ผู้บาดเจ็บต่อ 100 อุบัติเหตุ

**Formula:** `(SUM(รวมจำนวนผู้บาดเจ็บ) / nunique(ACC_CODE)) * 100`

------------------------------------------------------------------------

## 6. Dashboard Structure

Dashboard แบ่งเป็น **3 Tabs**

1.  **Tab 1 --- ภาพรวมสถานการณ์อุบัติเหตุ**
2.  **Tab 2 --- พื้นที่และปัจจัยการเกิดอุบัติเหตุ**
3.  **Tab 3 --- วิเคราะห์พื้นที่และช่วงเวลาความเสี่ยง**

ทุก Tab ใช้ Global Filter state เดียวกัน และ KPI/กราฟที่เกี่ยวข้องต้อง
re-calculate จาก filtered dataset

------------------------------------------------------------------------

# 7. Tab 1 --- ภาพรวมสถานการณ์อุบัติเหตุ

## Objective

ตอบว่า "สถานการณ์โดยรวมเป็นอย่างไร เกิดมากเมื่อใด และมีความรุนแรงเท่าใด"

## Top KPI Cards

แสดงอย่างน้อย: 1. จำนวนอุบัติเหตุ 2. จำนวนผู้เสียชีวิต 3. จำนวนผู้บาดเจ็บ 4. ผู้เสียชีวิตต่อ
100 อุบัติเหตุ

Optional: - ผู้บาดเจ็บสาหัส - เหตุที่มีผู้เสียชีวิต

## Recommended Visuals

### Visual 1 --- แนวโน้มอุบัติเหตุตามเวลา

**Chart:** Line chart\
**X:** เดือน/ปี\
**Y:** `nunique(ACC_CODE)`\
**Purpose:** ดู trend และช่วงที่จำนวนเหตุเพิ่ม/ลด

ควรให้ผู้ใช้สลับ granularity ระหว่าง Year / Month ได้หาก implementation รองรับ

### Visual 2 --- เปรียบเทียบ Accidents / Deaths / Injuries

**Chart:** Multi-series line chart หรือ grouped bar chart\
**Metrics:** accidents, deaths, injuries\
**Purpose:** เปรียบเทียบ pattern ของจำนวนเหตุและผลกระทบ

เนื่องจาก scale อาจต่างกันมาก ควรหลีกเลี่ยง dual-axis ที่ทำให้ตีความผิด หาก scale
ต่างกันมากให้ใช้ normalized view หรือ separate aligned charts

### Visual 3 --- อุบัติเหตุตามวันในสัปดาห์

**Chart:** Bar chart\
**X:** `day_of_week`\
**Y:** accident count

### Visual 4 --- Day × Hour Heatmap

**Chart:** Heatmap\
**X:** `hour`\
**Y:** `day_of_week`\
**Value:** accident count\
**Purpose:** ระบุช่วงวัน/เวลาที่เกิดเหตุสูง

### Visual 5 --- Severity composition

**Chart:** Grouped/stacked bar\
**Metrics:** deaths, serious injuries, minor injuries\
**Dimension:** month/year\
**Purpose:** ดูองค์ประกอบความรุนแรงตามเวลา

------------------------------------------------------------------------

# 8. Tab 2 --- พื้นที่และปัจจัยการเกิดอุบัติเหตุ

## Objective

ตอบว่า "เกิดที่ไหน และปัจจัย/บริบทใดพบร่วมกับอุบัติเหตุบ่อย"

## Recommended Visuals

### Visual 1 --- Province Map

**Preferred:** Thailand choropleth เมื่อมี province boundary/GeoJSON
ที่ตรวจสอบได้\
**Fallback:** Plotly point/scatter map จาก `LATITUDE` + `LONGITUDE`

**Metric selector:** accidents / deaths / injuries / deaths per 100
accidents

> หากยังไม่มี GeoJSON จังหวัดที่เชื่อถือได้ ห้ามสร้าง polygon จังหวัดเอง ให้ใช้ point map
> จากพิกัดจริงแทน

### Visual 2 --- จังหวัดที่มีจำนวนอุบัติเหตุสูง

**Chart:** Horizontal bar\
**Dimension:** `จังหวัด`\
**Metric:** `nunique(ACC_CODE)`\
**Default:** Top 10 หรือ Top 15

### Visual 3 --- จังหวัดที่มีผู้เสียชีวิตสูง

**Chart:** Horizontal bar\
**Dimension:** `จังหวัด`\
**Metric:** `SUM(ผู้เสียชีวิต)`

### Visual 4 --- มูลเหตุสันนิษฐาน

**Chart:** Horizontal bar\
**Dimension:** `มูลเหตุสันนิษฐาน`\
**Metric:** accident count\
**Optional selector:** accidents / deaths / serious injuries

### Visual 5 --- ประเภทยานพาหนะ

ใช้ข้อมูลจริงได้ 2 แนวทาง แต่ต้องระบุความหมายให้ชัด:

**A. Primary vehicle:** ใช้ `รถคันที่1` เพื่อดูประเภทของรถคันแรกที่บันทึกในเหตุ\
**B. Vehicle involvement:** sum คอลัมน์จำนวนรถแต่ละประเภท
เพื่อดูจำนวนยานพาหนะที่เกี่ยวข้อง

ห้ามตีความ `รถคันที่1` ว่าเป็น "รถที่เป็นฝ่ายผิด" หาก Dataset ไม่ได้ระบุเช่นนั้น

### Visual 6 --- บริเวณที่เกิดเหตุ

**Chart:** Bar chart\
**Dimension:** `บริเวณที่เกิดเหตุ`\
**Metric:** accident count

### Visual 7 --- ลักษณะการเกิดเหตุ

**Chart:** Bar chart\
**Dimension:** `ลักษณะการเกิดเหตุ`\
**Metric:** accident count / deaths

### Visual 8 --- สภาพอากาศ

**Chart:** Bar chart\
**Dimension:** `สภาพอากาศ`\
**Metric:** accident count / deaths

### Optional --- Route Hotspots

ใช้ `สายทาง`, `รหัสสายทาง`, `KM`, `LATITUDE`, `LONGITUDE` เมื่อข้อมูลสมบูรณ์พอ
เพื่อระบุสายทาง/ตำแหน่งที่มีเหตุสะสมสูง

------------------------------------------------------------------------

# 9. Tab 3 --- วิเคราะห์พื้นที่และช่วงเวลาความเสี่ยง

## Objective

ช่วยระบุ priority areas/time windows และค้นหา pattern ที่สัมพันธ์กับความรุนแรง
โดยไม่อ้าง causal inference

## Definition of "Risk" ใน Dashboard นี้

เนื่องจาก Dataset ไม่มี exposure denominator เช่น ปริมาณจราจร ระยะทางที่เดินทาง
หรือจำนวนประชากรผู้ใช้ถนน จึง **ห้ามเรียก accident count เพียงอย่างเดียวว่า
probability/risk rate**

ใน Dashboard ให้ใช้คำว่า: - **พื้นที่ที่มีเหตุสะสมสูง** - **พื้นที่ที่มีความรุนแรงสูง** -
**พื้นที่เฝ้าระวัง** - **ช่วงเวลาที่มีเหตุสูง**

หากใช้คำว่า "ความเสี่ยงสูง" ใน UI ต้องมี tooltip อธิบายว่าเป็น prioritization
จากจำนวนเหตุ/ความรุนแรงในข้อมูล TRAMS ไม่ใช่ exposure-adjusted risk
probability

## Recommended Visuals

### Visual 1 --- High-accident vs High-severity Province Matrix

**Chart:** Scatter plot\
**X:** accident count by province\
**Y:** deaths per 100 accidents\
**Bubble size:** total deaths หรือ injuries\
**Label:** province

**Purpose:** แยกจังหวัดที่: - จำนวนเหตุสูง + severity สูง - จำนวนเหตุสูง +
severity ต่ำกว่า - จำนวนเหตุต่ำกว่า + severity สูง

### Visual 2 --- Time Risk Heatmap

**Chart:** Heatmap\
**X:** hour/time_band\
**Y:** day_of_week\
**Metric selector:** accidents / deaths / serious injuries

### Visual 3 --- Cause × Severity

**Chart:** Bar/scatter\
**Dimension:** `มูลเหตุสันนิษฐาน`\
**Metrics:** accident count + deaths per 100 accidents หรือ
serious-injury events per 100 accidents

### Visual 4 --- Collision Type × Severity

**Dimension:** `ลักษณะการเกิดเหตุ`\
**Metric:** deaths per 100 accidents / serious injuries per 100
accidents

### Visual 5 --- Road Environment × Severity

**Dimension:** `บริเวณที่เกิดเหตุ`\
**Metric:** accident count + severity metric

### Visual 6 --- Weather × Severity

**Dimension:** `สภาพอากาศ`\
**Metric:** accident count + severity metric

### Visual 7 --- Key Insights Panel

สร้างข้อความสรุปจาก **filtered data จริง ณ เวลาที่ผู้ใช้ดู Dashboard** เช่น: -
จังหวัดที่มีจำนวนเหตุสูงสุดภายใต้ filter - จังหวัดที่มีผู้เสียชีวิตสูงสุด -
ช่วงเวลาที่มีจำนวนเหตุสูงสุด - มูลเหตุสันนิษฐานที่พบมากที่สุด - collision type ที่มี deaths
per 100 accidents สูงในกลุ่มที่มี sample size เพียงพอ

**ห้าม hard-code insight หรือตัวเลขตัวอย่าง**

เพื่อป้องกัน rate จากกลุ่มตัวอย่างเล็กมาก ควรแสดง severity ranking เฉพาะ category
ที่มี accident count ผ่าน threshold ที่กำหนดใน implementation และต้องระบุ
threshold ใน UI/technical notes

------------------------------------------------------------------------

## 10. Global Filters

แสดงเฉพาะ Filter ที่สร้างจาก field จริงและผ่าน data-quality check แล้ว

### Required

-   ปี
-   เดือน
-   จังหวัด
-   ช่วงเวลา (`time_band`)
-   มูลเหตุสันนิษฐาน
-   ประเภทยานพาหนะ (`รถคันที่1` หรือ normalized vehicle field)

### Recommended

-   หน่วยงาน / สายทางหน่วยงาน
-   ลักษณะการเกิดเหตุ
-   บริเวณที่เกิดเหตุ
-   สภาพอากาศ
-   ระดับความรุนแรงที่ derive จาก factual conditions เช่น has_death /
    has_serious_injury

### Conditional

-   ภูมิภาค --- ใช้ได้เมื่อมี province-to-region mapping ที่ตรวจสอบแล้ว
-   สายทาง --- ใช้เมื่อ category ไม่มากเกินไปหรือทำ searchable dropdown

### Filter Rules

1.  Filters เป็น multi-select ตามความเหมาะสม
2.  Filters ต้อง cascade หรือแสดงเฉพาะค่าที่ valid ภายใต้ selection
    ปัจจุบันเมื่อทำได้
3.  มี `Reset Filters`
4.  KPI และทุกกราฟที่เกี่ยวข้องต้องใช้ filtered dataframe เดียวกัน
5.  แสดงจำนวน records/events หลัง filter
6.  Missing/Unknown category ต้องจัดการอย่างโปร่งใส ไม่ silently map เป็น
    category อื่น

------------------------------------------------------------------------

## 11. Interaction Requirements

1.  Hover ทุกกราฟต้องแสดงค่าที่อ่านง่ายและชื่อ metric ชัดเจน
2.  Click/select จังหวัดหรือ category จากกราฟควรสามารถ cross-filter
    Dashboard ได้ หาก framework ที่ใช้รองรับอย่างเสถียร
3.  Global Filters มีผลกับทั้ง 3 Tabs
4.  เมื่อเปลี่ยน Filter แล้ว KPI และ charts ต้อง refresh จากข้อมูลเดียวกัน
5.  Map ต้องตอบสนองต่อ Global Filters
6.  Top-N chart ต้องคำนวณ ranking ใหม่หลัง Filter
7.  มี Reset/Clear Filters
8.  แสดง active filter summary หรือ badges เพื่อให้ผู้ใช้รู้ว่ากำลังดู subset ใด
9.  เมื่อ filtered data ว่าง ให้แสดง "ไม่พบข้อมูลตามเงื่อนไขที่เลือก" แทนกราฟผิดพลาด
10. Tooltip/Info icon ต้องอธิบาย metric ที่อาจตีความผิด เช่น deaths per 100
    accidents

------------------------------------------------------------------------

## 12. Logic / Calculation

### Accident Count

``` text
accidents = COUNT DISTINCT ACC_CODE
```

### Deaths

``` text
deaths = SUM(ผู้เสียชีวิต)
```

### Total Injuries

``` text
injuries = SUM(รวมจำนวนผู้บาดเจ็บ)
```

### Serious Injuries

``` text
serious_injuries = SUM(ผู้บาดเจ็บสาหัส)
```

### Minor Injuries

``` text
minor_injuries = SUM(ผู้บาดเจ็บเล็กน้อย)
```

### Casualties

``` text
casualties = deaths + injuries
```

### Fatal Accident

``` text
has_death = ผู้เสียชีวิต > 0
fatal_accidents = COUNT DISTINCT ACC_CODE WHERE has_death
```

### Deaths per 100 Accidents

``` text
death_rate_per_100_accidents = deaths / accidents * 100
```

### Serious Injuries per 100 Accidents

``` text
serious_injury_per_100_accidents = serious_injuries / accidents * 100
```

### Time Band

สร้างจาก `เวลา` หลัง parse สำเร็จ ตัวอย่าง grouping สามารถใช้: -
00:00--05:59 - 06:00--09:59 - 10:00--15:59 - 16:00--19:59 - 20:00--23:59

ช่วงดังกล่าวเป็น **analytical grouping ที่สร้างจากเวลาจริง** ไม่ใช่ category ต้นทาง
ต้องบันทึก logic ไว้ใน code และ UI documentation

### Province Severity Matrix

Aggregate by province:

``` text
province_accidents = nunique(ACC_CODE)
province_deaths = sum(ผู้เสียชีวิต)
province_deaths_per_100 = province_deaths / province_accidents * 100
```

### Data Grain

หลัง clean แล้ว main fact table ต้องรักษา grain เป็นหนึ่ง accident event ต่อ
`ACC_CODE` หรือมี logic deduplication ที่ชัดเจนก่อน aggregate

------------------------------------------------------------------------

## 13. Recommended Technical Architecture

### Backend

Python

### Data Processing

-   pandas
-   numpy (เฉพาะที่จำเป็น)

### Visualization

-   Plotly / Plotly Express

### Dashboard Framework

เลือก Python framework ที่รองรับ Plotly และ shared filters เช่น Dash หรือ
Streamlit โดย Antigravity เลือกตาม project environment ที่มีอยู่

### Suggested Files

``` text
project/
├─ data/
│  ├─ raw/
│  │  └─ trams/
│  └─ processed/
│     └─ accidents_trams_clean.csv
├─ src/
│  ├─ download_data.py
│  ├─ clean_trams.py
│  ├─ build_metrics.py
│  └─ data_validation.py
├─ app.py
├─ BRD.md
└─ ROAD_ACCIDENT_DASHBOARD_HANDOFF.md
```

------------------------------------------------------------------------

## 14. Data Source Display Requirements

Dashboard ต้องมีส่วน **Data Source / About the Data** ที่มองเห็นได้ชัดเจน
โดยระบุ:

**Primary source:** กระทรวงคมนาคม --- TRAMS\
https://datagov.mot.go.th/th/dataset/roadaccident

**TRAMS system:**\
https://trams.mot.go.th/

หากใช้ 3-base mortality: **Secondary source:** กรมควบคุมโรค ---
ระบบบูรณาการข้อมูลการตายจากอุบัติเหตุทางถนน (3 ฐาน)\
https://data.go.th/dataset/rtddi

ต้องแสดง: - ช่วงวันที่/ปีของข้อมูลที่โหลดจริง - วันที่ล่าสุดใน Dataset - Coverage note -
เวลาที่ Dashboard/data pipeline refresh ล่าสุด หากมีระบบ refresh

------------------------------------------------------------------------

## 15. Data Quality Requirements

ก่อนเปิดใช้ Dashboard ต้องตรวจอย่างน้อย:

1.  `ACC_CODE` null/duplicate
2.  row count เทียบกับ unique `ACC_CODE`
3.  parse `วันที่เกิดเหตุ` และ `เวลา`
4.  negative values ใน deaths/injuries ต้องไม่มีหรือถูก flag
5.  ตรวจ `รวมจำนวนผู้บาดเจ็บ` เทียบกับ serious + minor
6.  ตรวจ null rate ของจังหวัด สาเหตุ vehicle weather และ coordinates
    แยกตามปี
7.  ตรวจพิกัดที่อยู่นอกช่วงประเทศไทยโดยประมาณและ flag
8.  normalize whitespace/category spelling โดยเก็บ raw value เดิม
9.  ตรวจ schema ของทุก annual file ก่อน concat
10. ปีล่าสุดต้องระบุว่าเป็น partial/year-to-date หากข้อมูลยังไม่ครบปี
11. ห้ามเติมค่าที่หายด้วยค่าที่สมมติขึ้นเอง
12. ห้ามสร้าง category สาเหตุ/จังหวัด/vehicle ที่ไม่ปรากฏในข้อมูลหรือ mapping
    ที่ตรวจสอบแล้ว

------------------------------------------------------------------------

## 16. Limitations

1.  TRAMS ครอบคลุมโครงข่ายถนนของกระทรวงคมนาคม ไม่ใช่อุบัติเหตุทุกถนนในประเทศไทย
2.  `มูลเหตุสันนิษฐาน` ไม่ใช่ causal proof
3.  Missing values และความครบถ้วนของแต่ละ field อาจต่างกันระหว่างปี
4.  schema/category spelling อาจเปลี่ยนข้ามปี
5.  ปีล่าสุดอาจเป็นข้อมูลไม่ครบปี
6.  ไม่มี exposure denominator เช่น traffic volume / vehicle-km /
    population-at-risk จึงไม่สามารถคำนวณ accident probability หรือ
    exposure-adjusted risk ที่แท้จริงได้จาก TRAMS เพียงชุดเดียว
7.  การจัดอันดับด้วย rate ต้องระวัง category/province ที่มีจำนวนเหตุน้อย
8.  พิกัดบางรายการอาจ missing/invalid
9.  Secondary 3-base mortality เป็น person-level dataset และไม่สามารถ
    direct join กับ TRAMS แบบ event-level ได้
10. Dashboard ต้องแยกให้ชัดระหว่าง "จำนวนเหตุสูง" กับ "ความรุนแรงต่อเหตุสูง"

------------------------------------------------------------------------

## 17. Acceptance Criteria

Dashboard ถือว่าผ่านเมื่อครบทุกเงื่อนไขต่อไปนี้:

### Data

-   [ ] ใช้ข้อมูลจริงจาก TRAMS Open Data เท่านั้นสำหรับ main accident fact
-   [ ] ไม่มี mock/fake accident records
-   [ ] มีการเก็บ `ACC_CODE`
-   [ ] จำนวนอุบัติเหตุใช้ unique `ACC_CODE`
-   [ ] annual datasets ถูกตรวจ schema และ normalize ก่อน concat
-   [ ] มี data validation report หรือ validation logic ก่อน render
    Dashboard
-   [ ] แสดง data coverage และ latest available date

### Dashboard Structure

-   [ ] มี 3 Tabs ตาม BRD
-   [ ] Tab 1 แสดง overview + temporal patterns
-   [ ] Tab 2 แสดง geography + factors
-   [ ] Tab 3 แสดง priority/risk-pattern + severity analysis
-   [ ] มี KPI Cards ด้านบนตามบริบทของ Dashboard

### Filters & Interactivity

-   [ ] Global Filters ใช้ field ที่มีจริง
-   [ ] Filter ปี เดือน จังหวัด ช่วงเวลา สาเหตุ และ vehicle ใช้งานได้เมื่อ field
    มีข้อมูล
-   [ ] Filters มีผลต่อ KPI และ charts ที่เกี่ยวข้อง
-   [ ] Top-N/ranking คำนวณใหม่หลัง filter
-   [ ] Map อัปเดตตาม filter
-   [ ] มี Reset Filters
-   [ ] Empty selection/data state ไม่ทำให้ app error

### Metrics

-   [ ] Accidents = unique `ACC_CODE`
-   [ ] Deaths = sum `ผู้เสียชีวิต`
-   [ ] Injuries = sum `รวมจำนวนผู้บาดเจ็บ`
-   [ ] Severity rates มี denominator และ label ชัดเจน
-   [ ] ไม่มีการเรียก accident count ว่า exposure-adjusted risk
-   [ ] ไม่มี causal claim จาก `มูลเหตุสันนิษฐาน`

### Visuals

-   [ ] กราฟเลือกตามชนิดข้อมูลและอ่านง่าย
-   [ ] ไม่มีกราฟที่ต้องใช้ field ซึ่งไม่มีในข้อมูลจริง
-   [ ] Map ใช้พิกัดจริง หรือ verified province GeoJSON
-   [ ] Hover labels และ titles ระบุ metric/unit ชัดเจน
-   [ ] Category charts รองรับ Top-N/Other หรือ scrolling เมื่อ categories
    มาก

### Source & Transparency

-   [ ] มี Source ของข้อมูลใน Dashboard
-   [ ] มี coverage/limitation note
-   [ ] มีคำอธิบาย derived metrics
-   [ ] ปีล่าสุดที่ไม่ครบปีถูกระบุอย่างชัดเจน
-   [ ] ไม่มีตัวเลข จังหวัด สาเหตุ vehicle หรือ insight ที่ hard-code
    จากข้อมูลจำลอง

### Performance

-   [ ] Filter interaction ไม่ reload raw annual files ทุกครั้ง
-   [ ] ใช้ processed/cleaned dataset หรือ caching
-   [ ] Dashboard ทำงานได้กับข้อมูลหลายปีโดยไม่ค้างใน use case ปกติ

------------------------------------------------------------------------

## 18. Instructions for Antigravity

1.  อ่าน `BRD.md` และ `ROAD_ACCIDENT_DASHBOARD_HANDOFF.md` ก่อนแก้/สร้าง
    Dashboard
2.  ตรวจไฟล์ข้อมูลจริงและ `df.columns` ก่อนเขียน visualization
3.  ห้ามสร้าง mock data เพื่อให้ UI ดูสมบูรณ์
4.  ถ้า field ใน BRD ไม่มีในไฟล์จริง ให้:
    -   ไม่สร้างกราฟนั้น หรือ
    -   ใช้ fallback ที่ BRD อนุญาต และ
    -   บันทึกสิ่งที่ตัดออกไว้ใน project status
5.  สร้าง cleaning pipeline แยกจาก UI
6.  เก็บ raw columns และ derived/normalized columns แยกกัน
7.  ทุก KPI/chart ต้องรับ filtered dataframe/state เดียวกัน
8.  ตรวจ duplicate `ACC_CODE` ก่อนคำนวณจำนวนเหตุ
9.  แสดง Source + coverage note ใน UI
10. ห้าม hard-code Key Insights; ต้องคำนวณจาก filtered data จริง
11. ก่อนส่งงาน ให้ตรวจ Acceptance Criteria ทุกข้อ
12. หากต้องเพิ่ม GeoJSON, province-region mapping หรือ external dimension
    ให้ใช้แหล่งที่ตรวจสอบได้และบันทึก source เพิ่มใน documentation

------------------------------------------------------------------------

## 19. Definition of Done

งานถือว่าเสร็จเมื่อผู้ใช้สามารถเปิด Dashboard แล้วตอบได้จากข้อมูลจริงว่า:

-   ช่วงเวลาใดมีอุบัติเหตุสะสมสูง
-   จังหวัด/พื้นที่ใดมีจำนวนเหตุสูง
-   จังหวัด/พื้นที่ใดมีความรุนแรงต่อเหตุสูง
-   มูลเหตุสันนิษฐานใดพบมาก
-   vehicle/collision/road-environment/weather pattern
    ใดพบร่วมกับเหตุและความรุนแรง
-   จุดหรือสายทางใดควรพิจารณาเฝ้าระวังจากข้อมูลที่มี
-   ผลลัพธ์ทั้งหมดเปลี่ยนตาม Global Filters
-   ผู้ใช้สามารถตรวจสอบแหล่งข้อมูลและข้อจำกัดได้จาก Dashboard

**หลักสำคัญ:** Dashboard ต้องสื่อ "พื้นที่เสี่ยง -- ช่วงเวลาเสี่ยง -- ปัจจัยเสี่ยง --
ความรุนแรง" โดยไม่สร้างข้อมูลที่ไม่มีอยู่จริงและไม่ตีความเกินขอบเขตของ TRAMS
