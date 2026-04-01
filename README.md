# GE338_LAB_3
Lab 3: Machine Learning  Land Use / Land Cover Classification ด้วย Supervised &amp; Unsupervised ML

# Task 1: Training Strategy
▸ Land Cover Classes
กำหนด 5 คลาส ได้แก่

Water — แหล่งน้ำ เช่น แม่น้ำ คลอง บ่อ
Wetland Vegetation — พืชในพื้นที่ชุ่มน้ำ
Cropland — พื้นที่เกษตรกรรม
Built-up Area — พื้นที่สิ่งปลูกสร้าง
Bare Land — พื้นที่ดินเปลือย

คลาสถูกกำหนดให้ครอบคลุมลักษณะการใช้ที่ดินหลักในพื้นที่ศึกษาและมีความแตกต่างเชิง spectral เพียงพอสำหรับการจำแนก

▸ Training Sample Strategy

Training samples ถูกสร้างโดยการวาด polygon ด้วยตนเองจากภาพ Sentinel-2 โดยเลือกเฉพาะพื้นที่ที่มีลักษณะชัดเจน (homogeneous area)

มีการกระจาย polygon ในหลายตำแหน่งของพื้นที่ เพื่อให้ครอบคลุมความหลากหลายเชิงพื้นที่ และลด bias

ไม่ได้ใช้ training data จาก tutorial โดยตรง แต่สร้างใหม่ให้สอดคล้องกับบริบทพื้นที่ศึกษา

▸ Train / Validation Split

ใช้วิธี Random Split (80/20)

80% training
20% validation

ข้อดี: ใช้งานง่ายและเหมาะกับข้อมูลขนาดเล็ก
ข้อจำกัด: อาจเกิด spatial autocorrelation ทำให้ค่าความแม่นยำสูงเกินจริง

เมื่อเทียบกับ Spatial Cross-validation:
Spatial CV ลดการรั่วไหลของข้อมูลเชิงพื้นที่ และให้ผลที่ realistic มากกว่า แต่มีความซับซ้อนสูงกว่า

▸ Features Used
Spectral Bands:

B2, B3, B4, B8, B11, B12

Spectral Indices:
NDVI (vegetation)
NDWI (water)
NDBI (built-up)
NDMI (moisture)

เหตุผลทีเลือกใช้: indices ช่วยเพิ่มความสามารถในการแยกคลาส โดยเชื่อมโยงกับคุณสมบัติทางกายภาพของพื้นผิว

# Task 2: Algorithm Comparison
▸ Models
Random Forest (RF)
ทดลอง numberOfTrees = 50, 100, 200
จำนวนต้นไม้ที่มากขึ้นให้ผลลัพธ์ที่เสถียรมากขึ้น
Support Vector Machine (SVM)
ใช้เป็นโมเดลเปรียบเทียบ
▸ Evaluation Metrics

ใช้ตัวชี้วัด:

Overall Accuracy
Kappa Coefficient
Producer’s Accuracy
User’s Accuracy
F1-score ต่อคลาส

▸ Results
Random Forest ให้ความแม่นยำสูงกว่าและมีความเสถียรมากกว่า SVM
SVM ทำงานได้ดี แต่มีความไวต่อ distribution ของข้อมูล
การเพิ่ม spectral indices ช่วยเพิ่มความแม่นยำ โดยเฉพาะในคลาส vegetation และ water

# Task 3: Feature Importance
▸ Most Important Features
NDVI → แยก vegetation
NDWI → แยก water
SWIR bands → แยก built-up และ bare land

ผลลัพธ์สอดคล้องกับหลักการทางกายภาพของการสะท้อนแสง

▸ Effect of Removing Low-Importance Features

เมื่อทดลองตัด feature ที่มีความสำคัญต่ำออก
พบว่าความแม่นยำลดลงเล็กน้อย และบางคลาสถูกจำแนกได้แย่ลง

แสดงว่า feature หลายตัวมีบทบาทร่วมกัน (complementary)

# Task 4: Uncertainty Assessment
▸ Most Confused Classes

Cropland และ Wetland Vegetation เป็นคลาสที่สับสนมากที่สุด
เนื่องจากมีค่าการสะท้อนแสงใกล้เคียงกัน และอยู่ในพื้นที่ transition

▸ Areas of Uncertainty

พื้นที่ที่โมเดลไม่แน่ใจพบใน:

ขอบเขตระหว่างคลาส
พื้นที่ผสม (mixed pixels)
พื้นที่เกษตรที่มีน้ำขัง

ค่า probability ของหลายคลาสมีค่าใกล้เคียงกัน

▸ Reliability for Stakeholders

แผนที่มีความน่าเชื่อถือสูงในพื้นที่ที่มีลักษณะชัดเจน
แต่ควรใช้ด้วยความระมัดระวังในพื้นที่ transition

เหมาะสำหรับใช้เป็น decision-support tool มากกว่าการใช้งานเชิง absolute

# Discussion Questions
1. Effect of Increasing Training Samples
ตอบ การเพิ่ม training samples ส่งผลให้ความแม่นยำเพิ่มขึ้น แต่ไม่เพิ่มแบบเชิงเส้น (non-linear) เนื่องจากข้อมูลใหม่บางส่วนมีลักษณะซ้ำกับข้อมูลเดิม ทำให้ข้อมูลที่เพิ่มเข้ามาไม่ได้เพิ่ม “information ใหม่” อย่างมีนัยสำคัญ ในช่วงแรก การเพิ่ม sample จะช่วยลด variance ของโมเดลและทำให้ generalization ดีขึ้น แต่เมื่อข้อมูลเพียงพอแล้ว การเพิ่ม sample เพิ่มเติมจะให้ผลตอบแทนที่ลดลง 
2. Spatial Autocorrelation
ตอบ Spatial autocorrelation ทำให้ค่าความแม่นยำที่รายงานสูงเกินจริง เนื่องจากข้อมูล train และ test อยู่ใกล้กันในเชิงพื้นที่และมีลักษณะ spectral คล้ายกัน ส่งผลให้โมเดลเหมือน “เคยเห็นข้อมูลมาก่อน” แม้จะอยู่ในชุด test ทำให้ performance ที่วัดได้ไม่สะท้อนการใช้งานจริง วิธีที่เหมาะสมกว่าคือการใช้ spatial cross-validation เพื่อแยกข้อมูลตามพื้นที่
3. Worst Performing Class
ตอบ คลาสที่จำแนกได้แย่ที่สุดคือ Wetland Vegetation และ Cropland เนื่องจากมีความคล้ายคลึงกันทั้งในเชิง spectral และลักษณะทางกายภาพ
สาเหตุหลัก:
- ทั้งสองคลาสมีค่า NDVI สูง (มีพืชเหมือนกัน)
- มีความชื้นในดินสูง โดยเฉพาะในช่วงฤดูน้ำหลาก
- อยู่ในพื้นที่ transition ทำให้เกิด mixed pixels
- ความละเอียดของ Sentinel-2 (10 m) ไม่สามารถแยกโครงสร้างขนาดเล็กได้
ผลที่เกิดขึ้นในโมเดล:
- ค่า reflectance ใกล้กัน → decision boundary แยกยาก
- เกิด confusion สูงใน confusion matrix
แนวทางปรับปรุง:
- เพิ่ม training data โดยเฉพาะในพื้นที่ transition
- ใช้ temporal data (เช่น ภาพหลายช่วงเวลา) เพื่อแยกพฤติกรรมของพืช
- เพิ่ม index ที่เกี่ยวกับน้ำ เช่น NDWI หรือ NDMI
- ใช้ข้อมูล SAR (เช่น Sentinel-1) เพื่อแยกความชื้นของพื้นผิว
4. Transferability
▸ สิ่งที่ต้องปรับ
Training data ต้องสร้างใหม่ให้ตรงกับพื้นที่ใหม่
Class definition บางพื้นที่อาจมีประเภทที่ดินต่างกัน
Threshold หรือ parameter บางส่วน
▸ สิ่งที่สามารถใช้ซ้ำได้
Workflow (ขั้นตอน preprocessing → training → evaluation)
Feature extraction (bands + indices)
Model structure (RF, SVM)
แสดงให้เห็นว่า methodology มีความ generalizable แต่ต้องปรับตามบริบทพื้นที่

จุัดทำโดย นางสาว ณัฏฐณิชา ปลอดเหตุ 6506614038
