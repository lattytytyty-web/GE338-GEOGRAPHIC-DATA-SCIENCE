**Data inventory**

| Dataset                    | Source                | ใช้ตอบ | ข้อจำกัดที่รู้แล้ว              |
| -------------------------- | --------------------- | ------ | --------------------- |
| Sentinel-2 SR HARMONIZED              | European Space Agency |  Surface Reflectance   | - |
| Chlorophyll-a / Secchi / TSI | คำนวณจาก Sentinel-2 SR HARMONIZED               | คุณภาพน้ำ      | บางจุดใน ROI ไม่มีข้อมูล           |
| ตำแหน่งท่าเรือ             | open source data (OSM) | จุดออกเรือ     | ต้อง digitize เอง
| เส้นทางเดินเรือ              | open source data (OpenSeaMap)  | พื้นที่ที่ต้องเลี่ยง     | ต้อง digitize เอง        |
| ตำแหน่งแท่นขุดเจาะน้ำมัน              | open source data (Global Energy Monitor) | พื้นที่ที่ต้องเลี่ยง     | ต้อง digitize เอง        |
| ROI                        | shapefile             | ขอบเขตพื้นที่ศึกษา    | -              |


[**หลักฐานการดึงตำแหน่งท่าเรือจาก OSM**](https://gustkt.github.io/GE338-Lab-5/proposal/East_Port_OSM.png)


