# Model as a Service ด้วย Flask และ Docker

โปรเจ็คนี้เป็นส่วนหนึ่งของวิชา CPE312 Data Science
ให้บริการโมเดล Random Forest ที่ทำนายราคาบ้านเฉลี่ย (California Housing) ผ่านเว็บฟอร์มและ REST API โดยใช้ Flask + Gunicorn และรันใน Docker

## โครงสร้างโปรเจ็ค

```
ml_service/
├─ app.py              # Flask app (หน้าเว็บ, /health, /predict)
├─ train_model.py      # ฝึกและบันทึกโมเดลไว้ที่ model/model.pkl
├─ requirements.txt
├─ Dockerfile
├─ templates/
│  └─ main.html        # หน้าเว็บสำหรับกรอกค่าและดูผลทำนาย
└─ model/              # สร้างอัตโนมัติตอนฝึกโมเดล
```

## วิธี Build และ Run ด้วย Docker

```bash
docker build -t ds-ml-service .
docker run --rm -p 5000:5000 ds-ml-service
```

- หน้าเว็บ: http://127.0.0.1:5000/
- ตรวจสอบสถานะ: http://127.0.0.1:5000/health

หากพอร์ต 5000 ถูกใช้งานอยู่ ให้ใช้ `-p 8080:5000` แล้วเปิด http://127.0.0.1:8080/ แทน

## วิธีรันแบบไม่ใช้ Docker (ทดสอบ)

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
python train_model.py
python app.py
```

## ทดสอบ API

```bash
curl -X POST http://127.0.0.1:5000/predict \
  -H "Content-Type: application/json" \
  -d '{"features":[8.3,25.0,6.0,1.0,1200,3.0,34.2,-118.3]}'
```

PowerShell:

```powershell
Invoke-RestMethod -Uri http://127.0.0.1:5000/predict -Method Post -ContentType "application/json" -Body '{"features":[8.3,25.0,6.0,1.0,1200,3.0,34.2,-118.3]}'
```

ผลลัพธ์ที่ได้ เช่น `{"prediction": 4.029...}` (ประมาณ 4.03 หรือราว $403,000)

ลำดับฟีเจอร์: `MedInc, HouseAge, AveRooms, AveBedrms, Population, AveOccup, Latitude, Longitude`

## ภาพหน้าจอ

> แนบภาพหน้าจอหน้าเว็บที่ทำนายผลสำเร็จไว้ที่นี่ (เช่น `screenshot.png`)

![screenshot](screenshot.png)
