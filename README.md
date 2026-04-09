1. Problem Statement
AIIMS Delhi serves over 10,000 patients daily across a sprawling campus with 2,500+ beds and 7 major blocks. With an estimated fleet of 450 wheelchairs, the hospital faces a daily crisis that costs patients time, dignity, and in some cases, health outcomes.

The Core Problems
•	Patients and attendants spend 15–30 minutes searching for a wheelchair at OPD — often physically unfit to walk that far.
•	Staff have no visibility into wheelchair locations — they rely on memory and word of mouth.
•	Wheelchairs pile up in high-traffic zones (OPD) while critical zones (Trauma, ICU) run empty.
•	No tracking of damaged or missing wheelchairs — 30% of the fleet is unavailable on any given day.
•	No cleaning workflow — returned wheelchairs sit unclean in corridors.
•	Zero data for management — no utilization reports, no procurement planning, no audit trail.

  Impact: A post-surgery elderly patient alone at OPD Gate 3 cannot find a wheelchair. 8 wheelchairs sit idle in Ward B 200 metres away. Nobody knows.  

2. Solution Overview — WheelTrack AIIMS

WheelTrack is a complete AI-powered wheelchair lifecycle management system, simulated on the real AIIMS Delhi campus layout. It serves four distinct user roles through purpose-built interfaces, powered by four AI layers
AI Layers — The Intelligence Engine


WheelTrack uses four distinct AI layers, each with a clear input, model, and output. This is not AI as a buzzword — each layer solves a specific, demonstrable problem.

  AI Layer 1 — Demand Prediction (Facebook Prophet / LSTM)

•	Problem: OPD runs out of wheelchairs every morning at 8:15 AM. Nobody predicted it.
•	Input: Time of day, day of week, zone, historical request frequency, simulated patient footfall.
•	Model: Facebook Prophet time-series model (free, runs locally, no GPU needed).
•	Output: "OPD Block will need 40 wheelchairs in the next 30 min — move 15 from Ward Block now."
•	Demo moment: Show the forecast chart updating live as the simulator advances time. Watch the spike at 8 AM.

  AI Layer 2 — QR Heatmap + Smart Redistribution

•	Problem: Staff don't know where patients are congregating until it's too late.
•	Input: QR scan events — timestamp + zone location logged for every patient scan.
•	Model: Scan velocity detection — scans per minute crossing a threshold triggers demand spike alert.
•	Output: Push notification to staff: "High demand at OPD Gate 3 — move 5 wheelchairs from Ward B. Route: Corridor C → Gate 3 (4 min walk)."
•	Staff confirms: Marks done in app — system verifies via next scan pattern change.
•	Demo moment: Trigger 10 rapid QR scans at OPD — watch the staff dashboard light up with a move instruction.

  AI Layer 3 — Smart Priority Allocation

•	Problem: 3 simultaneous requests, 2 wheelchairs available. Who gets priority?
•	Input: Patient vulnerability score (age + condition + alone/with attendant), zone urgency, distance.
•	Model: Weighted scoring function — Trauma always wins, then elderly alone, then general.
•	Output: Trauma gets nearest wheelchair. OPD elderly patient gets second. Ward B goes to queue with ETA.
•	Vulnerability scoring: Patient taps 3 quick options at QR scan — Child/Adult/Senior, Post-surgery/Disabled/General, Alone/With attendant. Score computed in <100ms.
•	Demo moment: Submit 3 simultaneous requests — watch the AI assign in priority order instantly.

  AI Layer 4 — Predictive Maintenance + Anomaly Detection

•	Maintenance model: Each wheelchair has usage counter + age score. Logistic regression flags units likely to break in 7 days.
•	Anomaly detection: Isolation Forest flags wheelchairs stationary > 15 min in non-patient zones — possible theft/loss.
•	Output: "WC-047 stationary in Stairwell B for 4.2 hours — possible abandonment." and "WC-023, WC-107 flagged for preventive maintenance this week."
•	Condemned trigger: Admin marks wheelchair condemned — system auto-generates procurement request with fleet gap analysis.

wheeltrack-aiims/
├── backend/
│   ├── main.py              # FastAPI app + WebSocket server
│   ├── models.py            # SQLite schema + lifecycle states
│   ├── ai/
│   │   ├── demand_forecast.py   # Prophet model
│   │   ├── heatmap.py           # QR scan velocity detection
│   │   ├── priority.py          # Vulnerability scoring
│   │   └── maintenance.py       # Predictive maintenance + anomaly
│   └── simulator.py         # BLE wheelchair movement simulator
├── frontend/
│   ├── patient/             # QR scan mobile view (Hindi/English)
│   ├── staff/               # Desktop dashboard + floor map SVG
│   ├── housekeeping/        # Mobile cleaning queue
│   └── admin/               # Fleet health + audit trail
├── data/
│   └── aiims_layout.json    # Campus zone + QR point definitions
├── requirements.txt
└── README.md
