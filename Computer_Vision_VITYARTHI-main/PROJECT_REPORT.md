# Project Report – 
**Course**: Computer Vision

**Submission Type**: Bring Your Own Project (BYOP)

---

## 1. Problem Statement

India records one of the road accident rates in the world. A large number of these accidents involve two-wheelers. The main reasons for these crashes include helmet non-triple riding and signal jumping. Current enforcement by traffic police has limitations. It cannot cover all areas often suffers from error and is not available around the clock.

Most cities in India have traffic cameras. These are either basic CCTV systems with no AI capabilities or expensive proprietary systems that only a few can afford. This creates a need, for a smart low-cost solution using computer vision technology. Such a system should be able to do the following:

- Detect traffic violations and in real time
- Read and extract vehicle numbers for identification
- Generate an official challan (fine notice) without requiring any manual actions


---

## 2. Why This Problem Matters

- More than **1.5 lakh people** lose their lives each year in road accidents in India (MoRTH, 2022)

- **Helmet non-compliance** causes 45 percent of deaths on two‑wheelers

- ** riding** makes crashes much more serious

- Traffic police are short of staff. Automation can greatly boost their effectiveness

- E‑challan systems such, as iRASTE. Itms are already used in big cities but they are not open source and are hard to access

This project shows that a real computer‑vision powered traffic enforcement system can be built using simple open‑source tools and a little hardware.
---

## 3. Objectives

1. I want to build a real‑time violation detection system using a webcam or video feed.
2. I want to detect least three violation types using computer vision.
3. I want to extract vehicle number plates using OCR.
4. I want to generate a structured PDF e‑challan as evidence.
5. I want to present all information in a web dashboard.

---

## 4. Approach & Methodology

### 4.1 Object Detection — YOLOv8

I used YOLOv8n (nano variant) from Ultralytics as the primary detection backbone. It was chosen because:
- Pre-trained on COCO dataset. Detects person and motorcycle out of the box
- Fast for near-real-time inference (~30 FPS on CPU with nano model)
- Easy Python API with minimal setup

The detection pipeline:
1. Each frame from the webcam is passed to YOLOv8
2. Detected bounding boxes are filtered for person (class 0). Motorcycle (class 3)
3. Spatial overlap, between persons and motorcycles is calculated to associate riders with vehicles

### 4.2 Violation Logic

**No Helmet:**
I look at the 25 percent of each riders bounding box and call that the head region. I use a brightness test in the HSV Value channel to see whether a helmet is present. Helmets are usually darker and more in colour than bare heads. If the brightness is high that means the rider does not have a helmet.

**Triple Riding:**
I count how many people have bounding boxes that overlap a motorcycles bounding box by than 20 percent. If three or more people share one motorcycle I flag a riding violation.

**Red Light Jump:**
I examine the upper centre part of the frame, where traffic lights usually appear, in HSV colour space. If, than 4 percent of that area shows red pixels and I see a motorcycle I raise a red light jump violation.

### 4.3 Number Plate OCR

I crop the 20 percent of each motorcycle image to capture the number plate region. I then process this number plate region in the following steps:
1. I convert this number plate region to grayscale.
2. I enlarge this number plate region two times to improve OCR quality.
3. I binarise this number plate region using Otsus thresholding.
4. I feed this number plate region to pytesseract restricting the character set to A‑Z and 0‑9.
If pytesseract is not installed a fallback random plate generator guarantees that the demo still shows a number plate.

### 4.4 PDF Challan Generation
**fpdf2** was used to create PDF challans that include:
- Challan ID, time stamp, vehicle number, violation type
- AI confidence score
- Fine amount according to Motor Vehicles Act, 2019
- Evidence snapshot added to the PDF
- Authority footer and payment instructions

### 4.5 Dashboard

**Streamlit** was used for the frontend because it makes it easy to build data apps quickly using Python. The dashboard has the following features:
- A camera feed with real-time annotations
- A banner that shows alerts when violations are detected in real time
- Statistics about detections including total challans and counts, for each type of violation
- Settings to adjust the confidence threshold and choose which violations to monitor
- A log table that stores detection data and saves it as a CSV file
- Option to download challans in PDF format

---

## 5. Key Design Decisions

| Decision | Rationale |
|---|---|
| YOLOv8n (nano) over larger models | Speed > accuracy on CPU; nano is sufficient for proof-of-concept |
| Brightness heuristic for helmet | No custom-labelled dataset available; heuristic works as a baseline |
| HSV colour space for red light | More robust to lighting changes than RGB thresholding |
| fpdf2 over ReportLab | Simpler API, no external font dependencies, sufficient for structured documents |
| Streamlit over Flask/React | Faster to build; appropriate for a data-heavy CV dashboard |
| CSV logging over SQL | Simpler dependency stack; adequate for the project scope |
| Demo mode | Allows evaluation without physical camera hardware |

---

## 6. Challenges Faced

**Challenge 1 – No custom helmet dataset**
A custom helmet dataset could allow a custom trained YOLO model for helmet detection. That would make the accuracy much better. Because we do not have labelled data we used an approach. This is a known limitation and a clear direction for improvement.

**Challenge 2 – OCR accuracy on number plates**
Tesseract is made for printed text. It has trouble with low resolution, angled or dirty number plates. Upscaling and binarisation helped a bit. Accuracy in poor lighting remains a limitation. Dedicated ANPR models such as OpenALPR or PaddleOCR would work better.

**Challenge 3 – Red light detection without a signal in frame**
The colour based approach needs the traffic signal to be visible in the camera frame. In deployments, where the camera sits relative to the signal is very important.

**Challenge 4 – Real-time performance**
Running YOLOv8, OCR and PDF generation together in a loop caused latency. We fixed this by starting OCR and PDF generation when a violation is confirmed, not on every frame and, by using the YOLOv8 nano model.
---

## 7. Results

In demo mode the system worked well. Did the following:
- It found 3 simulated violations at the same time
- It created 3 different PDF challans with the right details
- All detection events were saved in a CSV file
- The dashboard showed real-time stats as events happened

With a webcam and good lighting:
- The system reliably detects people and motorcycles when confidence is above 50%
- Number plate OCR usually picks up part of the plate number
- light detection works fine if the signal is, in the upper center of the frame

---

## 8. What I Learned

1. **YOLOv8 is remarkably accessible**. YOLOv8 is remarkably accessible. Downloading a pretrained model and running inference takes than ten lines of Python. I found that the hard part is writing the application logic that uses the detections.

2. **Heuristics are a starting point**. Heuristics are a valid starting point. Not every computer vision problem needs a custom model. I used colour‑space analysis and geometric reasoning to solve problems effectively.

3. **OCR on real‑world images is hard**. OCR on real‑world images is hard. Controlled conditions such as lighting, a frontal angle and a clean plate are needed for reliable OCR. I felt humbled when this part of the project proved challenging.

4. **Streamlit is powerful for CV prototyping**. Streamlit is powerful for CV prototyping. Combining image display, controls and data tables in a Python file is very productive. I enjoyed laying everything out in one place.

5. **System design matters much as the model**. System design matters as much, as the model. Logging, evidence capture PDF generation and the dashboard together make this a usable system, not a detection script. I see how these pieces fit together to deliver value.

---

## 9. Future Improvements
- Train a custom YOLOv8 model using a dataset that has helmets and no helmets labeled (for example from Roboflow)
- Use an ANPR model (PaddleOCR or OpenALPR) to read license plates more accurately
- Link to the Vahan RTO database API to get information about the owner from the license plate number
- Send a message or email to the vehicle owner using Twilio or SendGrid
- Put everything on a Raspberry Pi with a Pi Camera, for edge deployment
- Include speed calculation by looking at how much the vehicle moves between frames

---

## 10. References

- Ultralytics YOLOv8 Documentation — https://docs.ultralytics.com
- OpenCV Documentation — https://docs.opencv.org
- pytesseract — https://pypi.org/project/pytesseract
- fpdf2 — https://py-fpdf2.readthedocs.io
- Motor Vehicles (Amendment) Act, 2019 — fine amounts
- MoRTH Road Accidents in India 2022 Report

---
