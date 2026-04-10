#Lab 3: Machine Learning สำหรับการจำแนกการใช้ที่ดิน

Land Use / Land Cover Classification (Ayutthaya Area)

## 📌 Overview
โปรเจกต์นี้เป็นการจำแนกประเภทการใช้ที่ดิน (Land Use / Land Cover) ในพื้นที่จังหวัดพระนครศรีอยุธยา ซึ่งเป็นพื้นที่ที่มี **พื้นที่ชุ่มน้ำ (wetland)** และ **พื้นที่เกษตรกรรม (paddy / upland agriculture)** อยู่ติดกัน

ใช้ข้อมูล Sentinel-2 และ Machine Learning (Random Forest) เพื่อสร้างแผนที่ และวิเคราะห์ความแม่นยำรวมถึงความไม่แน่นอนของโมเดล

---

## 🧠 ภารกิจที่ 1: Training Strategy

### 🔹 จำนวน Class และนิยาม
ใช้ทั้งหมด **6 Classes**:
1. Water — แม่น้ำ คลอง แหล่งน้ำถาวร  
2. Wetland — พื้นที่ชุ่มน้ำ น้ำท่วมขังบางช่วง  
3. Paddy — นาข้าว (น้ำ + พืช)  
4. Upland Agriculture — พืชไร่/เกษตรที่ไม่ท่วม  
5. Urban — สิ่งปลูกสร้าง เมือง  
6. Vegetation — ป่า/พืชธรรมชาติ  

---

### 🔹 การสร้าง Training Samples
- วาด Polygon เองจากภาพ Sentinel-2
- ตรวจสอบกับลักษณะพื้นที่จริง (เช่น pattern ของนาข้าว)

**เหตุผล:**  
เพื่อให้ training data สอดคล้องกับพื้นที่ศึกษา และแยก class ที่คล้ายกัน (wetland vs paddy)

---

### 🔹 การแบ่ง Train / Validation
ใช้ **80/20 random split**

**ข้อสังเกต:**  
วิธีนี้อาจทำให้ accuracy สูงเกินจริง เนื่องจากมี spatial autocorrelation

---

### 🔹 Features ที่ใช้
- Spectral Bands: B2, B3, B4, B8  
- Spectral Indices: NDVI, NDWI  

**เหตุผล:**  
- NDVI → แยก vegetation  
- NDWI → แยก water/wetland  
- NIR → แยกพืช  

---

## 🤖 ภารกิจที่ 2: Algorithm & Results

### 🔹 โมเดลที่ใช้
- Random Forest (200 trees)

---

### 🔹 Confusion Matrix (ผลจริง)

#### Accuracy ต่อ Class (จาก normalized matrix)

| Class | Accuracy |
|------|--------|
| Water | 0.87 |
| Wetland | 0.60 |
| Paddy | 0.78 |
| Upland Agri | 0.73 |
| Urban | 0.85 |
| Vegetation | 0.45 |

---

### 🔹 วิเคราะห์ผล

#### ✔ Class ที่ทำได้ดี
- Water (0.87) → แยกง่ายเพราะ spectral ต่างชัด
- Urban (0.85) → reflectance ต่างจากธรรมชาติ

#### ⚠ Class ที่ปานกลาง
- Paddy (0.78)
- Upland Agriculture (0.73)

➡ มีความสับสนกันเอง

#### ❌ Class ที่แย่ที่สุด
- Vegetation (0.45)
- Wetland (0.60)

---

## 📊 ภารกิจที่ 3: Feature Importance (ตีความจากผล)

แม้ไม่ได้แสดงกราฟ importance โดยตรง แต่จากผล:

- NDWI → สำคัญต่อ Water / Wetland
- NDVI → สำคัญต่อ Vegetation / Agriculture
- NIR → ช่วยแยกพืช

**สอดคล้องกับฟิสิกส์:**
- น้ำดูดกลืน NIR
- พืชสะท้อน NIR สูง

---

## ⚠️ ภารกิจที่ 4: Uncertainty Analysis

### 🔹 Class ที่สับสนมากที่สุด

#### 1. Wetland ↔ Vegetation
- Wetland → Vegetation = 0.30  
➡ พื้นที่ชุ่มน้ำที่มีพืชขึ้น → spectral คล้ายกัน

#### 2. Vegetation ↔ Upland Agri / Wetland
- Vegetation → Upland = 0.25  
- Vegetation → Wetland = 0.25  

➡ เป็น class ที่ model แยกไม่ชัด

---

### 🔹 พื้นที่ที่โมเดลไม่แน่ใจ
จากแผนที่ (ภาพที่แนบ):
- บริเวณ transition ระหว่าง:
  - นาข้าว ↔ พื้นที่ชุ่มน้ำ  
  - พืชธรรมชาติ ↔ เกษตร  

➡ เป็นพื้นที่ที่ probability ของ class ใกล้กัน

---

### 🔹 ความน่าเชื่อถือของแผนที่

สามารถใช้ได้ในระดับ:
- ✔ ภาพรวม (regional scale)
- ✔ วิเคราะห์แนวโน้มพื้นที่

ไม่เหมาะกับ:
- ❌ การตัดสินใจระดับจุด (pixel-level)

---

## 🔍 คำถามวิเคราะห์

### 1. เพิ่ม Training Samples 2 เท่า
คาดว่า accuracy จะเพิ่มขึ้นเล็กน้อย (~2–5%)

**เหตุผล:**
- model เรียนรู้ดีขึ้น
- แต่มี diminishing return

---

### 2. Spatial Autocorrelation
- ทำให้ accuracy สูงเกินจริง
- เพราะ train/test อยู่ใกล้กัน

➡ ควรใช้ spatial validation เพื่อประเมินจริง

---

### 3. Class ที่แย่ที่สุด
**Vegetation (0.45)**

#### วิธีแก้:
- เพิ่ม training samples
- ใช้ temporal data (หลายช่วงเวลา)
- เพิ่ม indices (เช่น EVI)

---

### 4. ถ้าทำพื้นที่อื่น

#### ต้องเปลี่ยน:
- training data
- class distribution

#### ใช้ซ้ำได้:
- workflow
- model (Random Forest)
- feature set

---

## 📉 Limitations

- Training data จำกัด
- ไม่มี ground truth จริง
- Class บางประเภท spectral overlap สูง
- ใช้ข้อมูลช่วงเวลาเดียว (no temporal info)

---

## ✅ Key Findings

- Random Forest ให้ผลดีแม้ data จำกัด
- Water และ Urban แยกได้ดีที่สุด
- Vegetation และ Wetland เป็น class ที่ยากที่สุด
- ความไม่แน่นอนสูงในพื้นที่ transition

---

