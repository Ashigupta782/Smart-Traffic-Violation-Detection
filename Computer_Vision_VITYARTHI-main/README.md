# 🚦 Smart-Traffic-Violation-Detection

An intelligent traffic enforcement system that uses **Computer Vision (YOLOv8)** to detect traffic violations in time and automatically generates **PDF e-challans**. Just like the systems used by traffic police, in India.

---

## 📸 Features

| Feature | Description |
|---|---|
| 👁️ **Person & Vehicle Detection** | YOLOv8 is a system that can find motorcycles and the people riding them as they happen in time.  |
| 🪖 **No Helmet Detection** | Identifies riders who are not wearing helmets. |
| 👥 **Triple Riding Detection** |Flags 3 or more persons, on a motorcycle. |
| 🚦 **Red Light Jump Detection** | Detects vehicles that cross during a signal. |
| 🔤 **Number Plate OCR** |Extracts vehicle registration numbers using Tesseract. |
| 🧾 **Auto PDF Challan** | Generates a PDF challan that includes evidence snapshots. |
| 📊 **Streamlit Dashboard** | Live feed, statistics, logs, and downloadable challans. |
| 🎭 **Demo Mode** | Works without a camera for demonstration purposes. |

---

## 🛠️ Tech Stack

- **Python 3.10+**
- **OpenCV** – Video capture & image processing
- **YOLOv8** (Ultralytics) – Object detection
- **pytesseract** – OCR for number plate reading
- **fpdf2** – PDF challan generation
- **Streamlit** – Web dashboard
- **Pandas** – Detection logging

---

## 🚀 Setup & Installation

### 1. Clone the repository
```bash
git clone https://github.com/Ashigupta782/Computer_Vision_VITYARTHI
```

### 2. Create a virtual environment (recommended)
```bash
python -m venv venv
source venv/bin/activate        # Linux/Mac
venv\Scripts\activate           # Windows
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Install Tesseract OCR (for number plate reading)
- **Windows**: Download from https://github.com/UB-Mannheim/tesseract/wiki
- **Linux**: `sudo apt install tesseract-ocr`
- **Mac**: `brew install tesseract`

### 5. Run the app
```bash
streamlit run app.py
```

Open your browser at `http://localhost:8501`

---

## 🎮 How to Use

### With a webcam:
1. Click **▶ Start Camera**
2. Point camera at traffic (or yourself to test)
3. Violations are detected automatically
4. PDF challans appear in the **Saved Challans** tab

### Without a webcam (Demo):
1. Click **🎭 Demo Mode**
2. The system simulates 3 violations
3. PDFs are generated instantly — download from the Challans tab

---

## 📁 Project Structure

```
e-challan-system/
│
├── app.py                  # Streamlit dashboard (main entry point)
├── detector.py             # YOLOv8 violation detection logic
├── challan_generator.py    # PDF challan generation (fpdf2)
│
├── snapshots/              # Auto-saved violation screenshots
├── challans/               # Generated PDF challans
├── logs/
│   └── detections.csv      # Detection history log
│
├── requirements.txt
└── README.md
```

---

## 🧠 How It Works

```
Live Camera Feed
      │
      ▼
YOLOv8 Object Detection
  ├── Detect motorcycles
  └── Detect persons
      │
      ▼
Violation Analysis
  ├── Count riders per motorcycle  →  Triple Riding
  ├── Analyse head region          →  No Helmet
  └── Check signal colour (HSV)   →  Red Light Jump
      │
      ▼
Number Plate OCR (pytesseract)
      │
      ▼
Generate PDF Challan (fpdf2)
      │
      ▼
Log to CSV + Display in Dashboard
```

---

## 📊 Violation Fine Chart

| Violation | Fine (INR) |
|---|---|
| No Helmet | ₹1,000 |
| Triple Riding | ₹1,000 |
| Red Light Jump | ₹5,000 |

---

## ⚠️ Limitations & Future Work

- Number plate OCR accuracy is affected by how clear the image's how bright the lighting is. Number plate OCR accuracy will improve if we use cameras and lighting.

- Helmet detection uses a brightness heuristic, which means it looks at how bright the area is. Helmet detection could be more accurate if we train a custom model.

- Red light detection needs the signal to be visible within the camera frame. Red light detection will fail if the signal is hidden or out of view.

- Future plans include sending SMS or Email alerts to the vehicle owner. Future integration, with the RTO database will allow real‑time updates.

---

## 📄 License

MIT License – free to use and modify.
