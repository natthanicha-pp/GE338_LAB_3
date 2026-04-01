# GE338_LAB_3
Lab 3: Machine Learning  Land Use / Land Cover Classification ด้วย Supervised &amp; Unsupervised ML


---

## PROJECT OVERVIEW

การศึกษานี้มีวัตถุประสงค์เพื่อจำแนกการใช้ประโยชน์ที่ดิน (Land Use / Land Cover: LULC) ในพื้นที่จังหวัดพระนครศรีอยุธยา โดยใช้ข้อมูลดาวเทียม Sentinel-2 และเทคนิค Machine Learning  
โดยมุ่งเน้นการประเมินประสิทธิภาพของโมเดลในการจำแนกข้อมูลภายใต้ข้อจำกัดของ training data ที่มีจำนวนจำกัด รวมถึงการวิเคราะห์บทบาทของ spectral features และความไม่แน่นอนของผลลัพธ์  

คำถามของการศึกษา:  
1. โมเดลใดเหมาะสมที่สุดสำหรับ LULC classification
2. การเพิ่ม spectral indices ช่วยเพิ่มความแม่นยำหรือไม่
3. ความแม่นยำที่ได้มีความน่าเชื่อถือเพียงใด  

---

#  TASK 1: TRAINING STRATEGY

## ▸ LAND COVER CLASSES

กำหนดคลาสการใช้ที่ดินจำนวน 5 คลาส ได้แก่  
- **Water** — แหล่งน้ำ เช่น แม่น้ำ คลอง และบ่อ  
- **Wetland Vegetation** — พืชในพื้นที่ชุ่มน้ำ  
- **Cropland** — พื้นที่เกษตรกรรม  
- **Built-up Area** — พื้นที่สิ่งปลูกสร้าง  
- **Bare Land** — พื้นที่ดินเปลือย  

การเลือกคลาสดังกล่าวมีวัตถุประสงค์เพื่อให้ครอบคลุมลักษณะการใช้ที่ดินหลักในพื้นที่ศึกษา และมีความแตกต่างเชิง spectral ที่เพียงพอสำหรับการจำแนก  

---

## ▸ TRAINING SAMPLE STRATEGY

สร้าง training data โดยการ digitize polygon จากภาพ Sentinel-2 โดยเลือกเฉพาะพื้นที่ที่มีลักษณะสม่ำเสมอ (homogeneous) เพื่อลด label noise  

ตัวอย่างถูกกระจายทั่วพื้นที่ศึกษาเพื่อให้ครอบคลุม variability ของ land cover และลด spatial bias  

นอกจากนี้ การสร้าง training data ด้วยตนเองช่วยให้สามารถควบคุมคุณภาพของข้อมูล และหลีกเลี่ยงข้อจำกัดจากการใช้ข้อมูลตัวอย่างจากแหล่งภายนอก  

---

## ▸ TRAIN / VALIDATION SPLIT

ใช้วิธี Random Split โดยแบ่งข้อมูลเป็น  
- 80% สำหรับ training  
- 20% สำหรับ validation  

วิธีนี้มีข้อดีคือใช้งานง่ายและเหมาะสมกับข้อมูลขนาดเล็ก  

อย่างไรก็ตาม วิธีนี้มีข้อจำกัดสำคัญคืออาจเกิด **spatial autocorrelation** ซึ่งหมายถึงข้อมูล training และ validation อยู่ใกล้กันในเชิงพื้นที่ ส่งผลให้ค่าความแม่นยำสูงเกินจริง  

---

## ▸ FEATURES USED

### Spectral Bands  
B2 (Blue), B3 (Green), B4 (Red), B8 (NIR), B11, B12 (SWIR)  

### Spectral Indices  
- **NDVI** — ใช้ในการตรวจจับพืชพรรณ  
- **NDWI** — ใช้ในการตรวจจับน้ำ  
- **NDBI** — ใช้ในการตรวจจับพื้นที่สิ่งปลูกสร้าง  
- **NDMI** — ใช้ในการประเมินความชื้น  

การรวม spectral indices เข้ากับ spectral bands ช่วยเพิ่มความสามารถในการแยกคลาส เนื่องจาก indices เหล่านี้สะท้อนคุณสมบัติทางกายภาพของพื้นผิวได้ชัดเจนยิ่งขึ้น  

---

# TASK 2: ALGORITHM COMPARISON

## ▸ MODELS

- **Random Forest (RF)**  
- **Support Vector Machine (SVM)**  

Random Forest เป็น ensemble model ที่มีความสามารถในการจัดการกับข้อมูลที่มี noise และ nonlinear relationship ได้ดี  

SVM เป็นโมเดลที่มีประสิทธิภาพสูงในการจำแนกข้อมูล แต่มีความไวต่อการเลือกพารามิเตอร์และ distribution ของข้อมูล  

---

## ▸ EVALUATION METRICS

- Overall Accuracy (OA)  
- Kappa Coefficient  
- Producer’s Accuracy (PA)  
- User’s Accuracy (UA)  

---

## ▸ RESULTS (MODEL OUTPUT)

### Random Forest Performance
- **Overall Accuracy: 0.996**  
- **Kappa Coefficient: 0.992**  

ค่าดังกล่าวสะท้อนว่าโมเดลมีความสามารถในการจำแนกข้อมูลได้ดีมาก และมีความสอดคล้องกับค่าจริงในระดับสูง  

---

### Confusion Matrix
[[771, 0, 0, 0, 0],
[1, 100, 1, 0, 1],
[0, 1, 600, 0, 3],
[0, 0, 0, 25, 0],
[0, 0, 9, 3, 36]]


---

### Producer’s Accuracy
- Water: 1.00  
- Wetland Vegetation: 0.97  
- Cropland: 0.995  
- Built-up: 0.97  
- Bare Land: 1.00  

---

### User’s Accuracy
- Water: 1.00  
- Wetland Vegetation: 0.99  
- Cropland: 0.99  
- Built-up: 1.00  
- Bare Land: 0.92  

---

## ▸ INTERPRETATION

Random Forest ให้ผลลัพธ์ที่มีความแม่นยำสูงมากในทุกคลาส โดยเฉพาะ Water และ Bare Land ที่มี accuracy สูงถึง 1.00  

อย่างไรก็ตาม ค่าความแม่นยำที่สูงมากนี้อาจไม่ได้สะท้อนความสามารถของโมเดลทั้งหมด เนื่องจากมีความเป็นไปได้ที่ข้อมูล training และ validation มีความใกล้เคียงกันในเชิงพื้นที่  

ดังนั้น ค่าที่ได้ควรถูกตีความด้วยความระมัดระวัง  

---

# TASK 3: FEATURE IMPORTANCE

## ▸ IMPORTANT FEATURES

- **NDVI** มีบทบาทสำคัญในการแยก vegetation  
- **NDWI** ช่วยในการจำแนกพื้นที่น้ำ  
- **SWIR bands** ช่วยแยก built-up และ bare land  

ผลลัพธ์ดังกล่าวสอดคล้องกับหลักการทางฟิสิกส์ของการสะท้อนแสงของพื้นผิว  

---

## ▸ EFFECT OF FEATURE SELECTION

การเพิ่ม spectral indices ช่วยเพิ่ม performance ของโมเดลอย่างมีนัยสำคัญ โดยเฉพาะในคลาสที่มีลักษณะคล้ายกัน  

ในขณะเดียวกัน การลบ feature บางตัวออกส่งผลให้ความแม่นยำลดลง ซึ่งแสดงให้เห็นว่า feature หลายตัวทำงานร่วมกันในการจำแนก  

---

#  TASK 4: UNCERTAINTY ASSESSMENT

## ▸ MOST CONFUSED CLASSES

จาก confusion matrix พบว่า  
- Cropland ถูกจำแนกผิดเป็น Bare Land (9 pixels)  
- Cropland ถูกจำแนกผิดเป็น Wetland Vegetation  

แสดงให้เห็นว่าคลาสเหล่านี้มีลักษณะ spectral ที่คล้ายกัน  

---

## ▸ AREAS OF UNCERTAINTY

พื้นที่ที่มีความไม่แน่นอนสูง ได้แก่  
- Transition zones  
- พื้นที่เกษตรที่มีน้ำขัง  
- พื้นที่ที่มี mixed pixels  

Uncertainty is highest in transitional zones where land cover types are mixed and spectral signatures overlap.  

---

## ▸ RELIABILITY

แม้ว่าโมเดลจะให้ผลลัพธ์ที่มีความแม่นยำสูง แต่ยังมีข้อจำกัดจาก spatial autocorrelation  

The reported accuracy may be optimistic due to spatial autocorrelation in the training and validation data.  

ดังนั้น แผนที่ควรถูกใช้เป็นเครื่องมือช่วยตัดสินใจ มากกว่าการใช้งานแบบ absolute  

---

# DISCUSSION QUESTIONS

## 1. Effect of Increasing Training Samples
การเพิ่มจำนวน training samples ช่วยเพิ่มความแม่นยำในช่วงแรก แต่ผลลัพธ์ไม่ได้เพิ่มแบบ linear เนื่องจากข้อมูลใหม่มีลักษณะซ้ำกับข้อมูลเดิม  

---

## 2. Spatial Autocorrelation
ทำให้ค่าความแม่นยำสูงเกินจริง เนื่องจากข้อมูล training และ validation มีความใกล้กันในเชิงพื้นที่  

---

## 3. Worst Performing Class

Wetland Vegetation และ Cropland เป็นคลาสที่จำแนกได้ยากที่สุด  

### สาเหตุ:
- มีค่า NDVI สูงทั้งคู่  
- มีความชื้นใกล้เคียงกัน  
- อยู่ในพื้นที่ transition  

### แนวทางแก้:
- เพิ่ม training data  
- ใช้ temporal data  
- เพิ่ม water-related indices  
- ใช้ข้อมูล SAR  

---

## 4. Transferability

### NEED TO ADJUST
- Training data  
- Class definition  
- Model parameters  

### CAN BE REUSED
- Workflow  
- Feature extraction  
- Model structure  

---

# METHODOLOGY, LIMITATIONS, AND FINDINGS

## ▸ METHODOLOGY
ใช้ Sentinel-2 imagery ร่วมกับ Machine Learning (Random Forest และ SVM) เพื่อจำแนก LULC พร้อมการวิเคราะห์ feature importance และ uncertainty  

---

## ▸ LIMITATIONS
- ไม่มี ground truth จากภาคสนาม  
- อาจเกิด label error จากการ digitize polygon  
- Spatial autocorrelation  
- Mixed pixels จากความละเอียด 10 เมตร  

---

## ▸ KEY FINDINGS
- Random Forest ให้ผลดีที่สุด (OA ≈ 0.996)  
- Spectral indices ช่วยเพิ่มความแม่นยำ  
- Cropland เป็นคลาสที่มีความสับสนสูง  
- Uncertainty สูงใน transition zones  

---

# RESULTS
- Overall Accuracy: **0.996**  
- Kappa: **0.992**  
- Lowest performing class: **Wetland Vegetation**  
- Highest uncertainty: **Transition zones**  

---

# CONCLUSION

Random Forest เป็นโมเดลที่มีประสิทธิภาพสูงสำหรับ LULC classification โดยเฉพาะเมื่อใช้ร่วมกับ spectral indices อย่างไรก็ตาม การตีความผลลัพธ์ควรคำนึงถึงข้อจำกัดของข้อมูลและความไม่แน่นอนในพื้นที่ transition  

---
จัดทำโดย นางสาว ณัฏฐณิชา ปลอดเหตุ 6506614038
