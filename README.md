# Tokyo Olympic 2021 ETL
Table of Content
- Description
- Tech Stack
- Prerequisite
- How to execute project
  - Setup storage account
  - Loading data from github to datalake
  - Databricks Config
  - Data factories and Databricks integration
  - Execute Pipeline
- Credit

## Description
โปรเจคมีจุดประสงค์เพื่อฝึกใช้งาน tools ต่าง ๆ ใน Azure โดยที่โปรเจคนี้นี้เป็นการทำ ETL Pipeline ด้วย Azure ซึ่ง ETL นี้จะดึงข้อมูล Tokyo Olympic 2021 ซึ่ง host อยู่บน github นี้ไปใส่ใน Datalake จากนั้นจึงนำข้อมูลใน Datalake มา Transform แล้วเก็บไว้ใน Datalake เพื่อให้สามารถดึงไปทำการ Analytic ได้ 

## Tech Stack
- Azure Storage
- Azure DataFactory
- Azure DataBricks
- Azure Key-Vault
- PySpark
## Prerequisite
- ต้องมี Microsoft Azure account ที่มีเครติตเพียงพอต่อการใช้งาน
## How to execute project
### Setup Storage account
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

### Building pipeline
3. ไปที่ Data factories เพื่อทำการสร้าง pipeline สำหรับการทำ ETL ซึ่งการ config จะคล้ายกับตอนสร้าง storage account โดย config เฉพาะในส่วน Basics เท่านั้นจากนั้นกด next ไปเรื่อย ๆ จนกว่าจะสร้าง resource เสร็จ
> note : เลือก Resource group เดียวกับตอนสร้าง storage account และ version ในโปรเจคนี้จะใช้เป็น V2

![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/fc37487a-8972-49c9-8a38-3c33f603346e)
เมื่อ Data factories สร้างเสร็จแล้วให้กด go to resource จากนั้นกด launch studio เพื่อเปิด Data factories studio ขึ้นมาสำหรับใช้ทำ pipeline

4. เมื่อเข้ามาที่หน้า Studio แล้วให้ทำการกดปุ่ม New แล้วเลือก pipeline จากนั้นจะเข้ามาสู่หน้า Studio ที่ใช้ในการสร้าง pipeline แล้วเลือก Copy data (อยู่ใน Activities >> Move and Transform) มาวางลงบน pipeline 
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/3a4a73ad-488b-4abf-99eb-1294509ac6ce)
5. หลังจากนั้นให้ไป copy url ของ raw file จากโฟลเดอร์ data ใน github นี้โดยกดไปที่ไฟล์ที่ต้องการแล้วกดปุ่ม raw จากนั้น copy url ของเว็บที่พึ่งเปิดขึ้นมาซึ่งเราจะใช้ url ของ raw file นี้เป็น data source สำหรับโปรเจคนี้
- ![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/039ad9cc-c282-4e34-abad-80d8e857ec21)
- ![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/0025ea56-25df-4f86-aa02-e18dbc734a26)
6. จากนั้นกลับมาที่ data factories studio เพื่อที่จะสร้าง connection กับ data source โดยกดที่ source >> new ต่อมาในช่องค้นหาให้พิมว่า HTTP แล้วเลือก source เป็น HTTP และต่อมาให้เลือก format เป็น CSV
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/e560db35-2a4a-4082-a251-2675447d7e9b)
เมื่อเลือก format เสร็จแล้วให้ทำการสร้าง linked service เพื่อเชื่อมต่อ raw file บน github กับ data factories โดยกดเลือกที่ linked service จากนั้นกดปุ่ม new เพื่อสร้าง linked service อันใหม่ โดยมี config ดังนี้
**name** - เปลี่ยนเป็น**ชื่อของ linked service** ที่ต้องการ
**Base url** - เอา **link จาก raw file** ที่ copy มาใส่
**Authentication type** - ให้เปลี่ยนเป็น **anonymous** เพื่อง่ายต่อการใช้งาน 
เมื่อ config เสร็จแล้วสามารถกดปุ่ม test connection ที่มุมขวาล่างเพื่อทดสอบว่าเชื่อมต่อได้หรือไม่ หากเชื่อมต่อได้ให้กด create 
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/cc69e5c4-c410-4407-bcb3-c40d448e58e3)
7. เมื่อ connection source เสร็จแล้วให้มา sink data factories กับ data factories โดยกดที่ sink >> new  แล้วเลือก Azure Data Lake Storage Gen2 >> CSV หลังจากนั้นให้สร้าง linked service สำหรับ connect กับ data lake เพื่อใช้เก็บข้อมูลที่ดึงมาจาก source โดยตั้งชื่อให้กับ linked service นี้ และเลือก Azure subscription กับ Storage account เป็นอันเดียวกับที่ใช้ทำโปรเจคนี้ จากนั้นกด test connection หากสำเร็จให้กด create เพื่อสร้าง sink ได้เลย
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/e10e58cb-cbf4-4226-a794-6803f42516d9)

หลังจากสร้าง linked service เสร็จแล้วให้เลือก path ที่จะนำ data ไปเก็บไว โดยให้เลือกไปที่ path ของ raw data จากนั้นช่องสุดท้ายให้ตั้งชื่อไฟล์ที่จะ load เข้ามาเก็บใน datalake ส่วน import schema ให้เลือก none เพียงเท่านี้ก็สามารถเชื่อมต่อไปยัง datalake ได้แล้ว
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/7a75e987-9b13-4dc7-aa3e-7a64e9e4d2c0)
8. สร้าง copy data บน pipeline ให้ครบทุกไฟล์ใน folder data โดยสร้าง source ใหม่ในทุก ไฟล์ แต่สามารถใช้ sink เดิมได้เพราะทุกไฟล์จะถูก load ไปเก็บที่เดียวกันไม่ต้องสร้างใหม่ เมื่อสร้างเสร็จแล้วจะได้ pipeline ดังนี้
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/5837a1fa-6dfe-4020-bc9f-8e61a0da2be3) 
กดที่ data copy แล้วกดที่ลูกสรที่เขียนว่า on completion จากนั้นลากมายัง data copy ตัวต่อไปเพื่อเป็นการกำหนดว่าหาก load ข้อมูลเสร็จจาก copy data ตัวนี้แล้วให้ไปทำ copy data ตัวต่อไป โดยทำให้ครบทุกตัว จนถึงตอนนี้เราได้ pipeline ที่จะทำการ load data จาก raw file บน github มาไว้ใน datalake แล้ว

### Databricks config
9. ไปที่ App registration เพื่อทำการ registration โดยให้ใส่แค่ชื่อ app จากนั้นกด create ได้เลย เมื่อสร้างเสร็จแล้วให้กดเข้าไปที่ app registration ที่พึ่งสร้างเสร็จจากนั้นจด **Application (client) ID** และ **Directory (tenant) ID** ลงใน notepad ต่อมาให้ไปที่ **Certificates & Secret** แล้วทำการสร้าง secret พอสร้างเสร็จแล้วให้จดค่า value ของ secret ไว้ใน notepad
10. ไปที่ container (ที่ใช้สำหรับเก็บข้อมูลในโปรเจคนี้) >> Access Control (IAM) >> Add >> Add Role assignment จากนั้นเลือก role เป็น **Storage Blob Data Contributor** ต่อมากดที่ select members แล้วในช่องค้นหาทางด้านขวาให้ค้นหาชื่อ App registration ที่พึ่งทำการสร้างไปแล้วกดเลือก จากนั้นกดปุ่ม review + assign เป็นการแอด role ให้กับ App registration เรียบร้อย
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/33aaedc8-74c6-43bf-a216-e654979cbe8d)
> **ที่ต้อง assign role เพราะว่าเวลาที่ใช้งาน Databricks เราจะ mount data จาก storage ไปยัง Databricks ผ่านทาง App registration ดังนั้นหาก App registration ที่เราสร้างไม่มี role ในการเข้าถึงข้อมูลก็จะไม่สามารถ mount data ไปยัง data bricks ได้**

11. ไปที่ Azure databricks จากนั้นสร้าง Databricks ขึ้นมาโดย config แค่หน้าแรกเท่านั้นแล้วกด review + create เมื่อสร้างเสร็จแล้วให้ไปที่ Databricks ที่สร้างไว้แล้วกด launch workspace หลังจากนั้นไปที่ **compute >> create compute** ในหน้า Create ให้ใช้ config เป็น default จาก Databricks แต่เปลี่ยนจาก Multi node เป็น Single node
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/dadc308f-861f-430a-8906-3988ac83d3be)
เมื่อสร้าง Cluster เสร็จแล้วให้ไปที่ navigation ทางด้านซ้ายจากนั้นกดไปที่ workspace แล้วไปที่ folder ตามภาพด้านล่างจากนั้นกด import เพื่อนำ file transformation ที่อยู่ใน github นี้ไปวางไว้
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/ba1ff83a-ca08-4c1e-b235-52a3f7d1c412)
> **ซึ่งในตอนนี้จะยัง run ไม่ได้เพราะ code ที่แปะไว้บน github มีการใช้ Azure key vault เพื่อป้องกันไม่ให้ credential อยู่บน code ดังนั้นจึงต้องทำการ config key vault ก่อนจึงจะสามารถใช้งานได้**
แต่หากไม่ต้องการใช้ Azure key vault สามารถนำ credential ที่จดอยู่ใน notepad มาวางบน code ตาม link นี้ได้เลย - https://docs.databricks.com/en/storage/azure-storage.html

### Key vault config
12. ไปที่ Key vault จากนั้นสร้าง Key vault ขึ้นมาโดย config แค่หน้าแรกเท่านั้นจากนั้นกด Review + create เมื่อสร้างเสร็จแล้วให้ไปที่ Key vault ที่พึ่งสร้างเสร็จแล้วไปที่ **secrets >> Generate/Import** แล้วทำการสร้าง secret ของ **Application (client) ID , Directory (tenant) ID และ Secret ของ App registration** เมื่อใส่ชื่อของ secret กับ value(Credential) เสร็จแล้วทำการกด create ได้เลย
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/8dcb6537-42b2-4da6-99e0-84acde10a880)
จากนั้นไปที่ Access Control (IAM) >> Add >> Add Role assignment จากนั้นเลือก role เป็น **Key Vault Administrator** ต่อมากดที่ select members แล้วในช่องค้นหาทางด้านขวาให้ค้นหาชื่อ **AzureDataBricks** ซึ่งนี่จะเป็นการ assign role ให้ databricks สามารถมาดึง key จาก Key vault ไปใช้ได้
13. กลับไปที่ตัว Azure DataBricks เพื่อไปสร้าง scope ที่จะสามารถใช้งาน Key vault ได้โดยไปที่ **https://<databricksInstance>#secrets/createScope** (ใน databricks instance จะจบด้วย o=1231531... ให้เติม #secrets/createScope ตามท้ายเพื่อเข้าไปหน้าในการสร้าง scope)
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/3ed60778-4192-4c19-8ba0-2b1b497aa7ce)
> **DNS Name** ให้ใส่ Vault uri และ **Resource ID** ให้ใส่ Resource ID โดยสามารถหาได้จาก key vualt(ที่ใช้กับโปรเจคนี้) >> properties เมื่อ config เสร็จแล้วให้กด create

14. ไปที่ User setting >> Developer >> Access token >> Manage จากนั้นกด generate token แล้วจดรหัส token ที่ได้ลงใน notepad
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/69966e8f-f614-4e13-8a8d-269c4cd809ff)

### Data factories and Databricks integration
15. กลับไปที่ Data factories ให้ไปที่ Activities >> Databricks >> Notebooks จากนั้นลาก notebooks ลงมาใน workspace แล้วกดสร้าง linked service ใหม่สำหรับ notebook โดยมี config ดังต่อไปนี้
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/d6705d8d-40c3-4f31-aea9-c85762be1f39)
    1. เลือก Subscription และ Workspace
    2. Select cluster เป็น Existing cluster
    3. Authentication type เป็น Access token
    4. ใส่ token ที่ได้มาจาก Databricks ลงไป
    5. เลือก cluster ที่พึ่งสร้างมา
จากนั้นกด test connection หากสำเร็จให้กด create
16. ไปที่ settings แล้วทำการเลือก path ของไฟล์ที่ใช้ในการ Transform จากนั้นให้ลากเส้นเชื่อมระหว่าง Copy data อันสุดท้ายทำให้หลังจากที่ load data เสร็จแล้วก็จะมาทำการ Transform ต่อ โดยเมื่อเสร็จขั้นตอนที่ 16 แล้วจะได้ pipeline ดังภาพด้านล่าง
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/92459555-d731-4038-aad0-167281ef5eeb)

### Execute Pipeline
17. ไปที่ Data factories >> Add trigger แล้วกด Trigger now
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/a2dcbd43-5218-4fa2-8e14-51f1c344f479)
18. สามารถไป monitor pipeline ได้โดยไปที่ monitor ทางด้านซ้ายแล้วกดไปที่ pipeline ที่กำลัง run อยู่
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/f5b220c3-bc7d-42ff-95eb-1b9cc0afb1c6)
19. หาก pipeline run สำเร็จจะเป็นเหมือนภาพด้านล่าง เมื่อ pipeline ทำงานเสร็จเราจะได้ data ที่ Transform แล้วอยู่ใน transformData พร้อมที่จะนำไปทำการ analytic ต่อ
![image](https://github.com/Zhuuuun/TokyoOlympic2021ETL/assets/96523298/f707bb5a-60ab-4a58-a337-5a13d6926bff)

## Credits
**Inspired of this project** 
  - https://www.youtube.com/watch?v=IaA9YNlg5hM&t=2663s

**Connect Azure Databricks with Azure Key vault**
  - https://learn.microsoft.com/en-us/answers/questions/633235/databricks-support-azure-keyvault-backed-secret-sc
  - https://www.youtube.com/watch?v=ul4Gqehas0w&t=822s
