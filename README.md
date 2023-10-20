# Tokyo Olympic 2021 ETL
Table of Content
- Description
- Tech Stack
- Prerequisite
- How to execute project
- Credit

## Description
โปรเจคนี้เป็นการทำ ETL Pipeline เพื่อ

## Tech Stack
- Azure Storage
- Azure DataFactory
- Azure DataBricks
- Azure Key-Vault
- Azure Synapse 
- PySpark
## Prerequisite
- ต้องมี Microsoft Azure account ที่มีเครติตเพียงพอต่อการใช้งาน
## How to execute project
1. สร้าง Storage account สำหรับใช้ใน service ต่าง ๆ ในการทำโปรเจค โดยเข้าไปที่ https://portal.azure.com จากนั้นค้นหา service ที่ชื่อว่า **storage account** แล้วกดที่ปุ่ม **create**
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/1d684a19-a7b2-4600-b19e-797ecc318553)
- **Subscription** - เลือก Subscription เพื่อใช้สำหรับการชำระเงินค่าบริการจากการใช้ service  
- **Resource group** - group ของ service ที่ใช้สำหรับโปรเจคนี้
- **Storage account name** - ชื่อของ Storage account โดยชื่อที่ตั้งจะต้อง unique 
- **Region** --- location ของที่เก็บข้อมูลบน Azure มีหลายที่ให้เลือกแต่ค่าบริการจะแตกต่างกันออกไป แนะนำให้เลือก region ที่ใกล้กับเรามากที่สุดเพื่อจะได้ประหยัด cost 
- **Performance** - มี 2 ระดับให้เลือกคือ premium และ standard โดย premium จะเหมาะกับโปรเจคที่ต้องการ latency (delay ในการส่งข้อมูล) ต่ำ แต่ในโปรเจคนี้แนะนำให้ใช้ **standard** เพราะไม่ได้ต้องการความไวในการส่งข้อมูลมาก
- **Redundancy** - การ Backup ข้อมูลบน Azure จะมีตั้งแต่ copy data หลาย ๆ ชุดบน datacenter เดียวไปจนถึงการ Backup ข้าม region เลยก็ได้ซึ่งในโปรเจคนี้แนะนำให้เลือก **GRS (Geo-redundant storage)** 
เมื่อทำการ config เสร็จแล้วให้กด next ไปจนกว่าจะสร้าง resource เสร็จ
2. เมื่อ Storage account สร้างเสร็จแล้วให้ไปที่หน้า Storage account ที่พึ่งสร้างเสร็จจากนั้นให้ไปที่ container เพื่อสร้าง container สำหรับใช้เก็บ data ในโปรเจคนี้
เมื่อสร้าง container เสร็จแล้วให้กดเข้ามาข้างใน container เพื่อสร้าง **directory ไว้สำหรับเก็บข้อมูลดิบที่ดึงมาจาก source (rawData)**  **และ directory ไว้สำหรับเก็บข้อมูลที่ Transform แล้ว (transformData)**
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/0e39729a-5548-4eae-b195-9fb44e9ca705)
3. ไปที่ Data factories เพื่อทำการสร้าง pipeline สำหรับการทำ ETL ซึ่งการ config จะคล้ายกับตอนสร้าง storage account โดย config เฉพาะในส่วน Basics เท่านั้นจากนั้นกด next ไปเรื่อย ๆ จนกว่าจะสร้าง resource เสร็จ
> note : เลือก Resource group เดียวกับตอนสร้าง storage account และ version ในโปรเจคนี้จะใช้เป็น V2

![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/fc37487a-8972-49c9-8a38-3c33f603346e)
เมื่อ Data factories สร้างเสร็จแล้วให้กด go to resource จากนั้นกด launch studio เพื่อเปิด Data factories studio ขึ้นมาสำหรับใช้ทำ pipeline
